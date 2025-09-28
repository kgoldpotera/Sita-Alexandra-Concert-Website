<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { base } from '$app/paths';

	// Accept string URL (e.g. from /static), or a File/Blob (for previews)
	export let src: string | File | Blob;
	/** leave empty for decorative image (no visible text) */
	export let alt: string = '';
	/** e.g. '16/9', '4/3' or a number. If omitted, height auto-sizes from the image. */
	export let ratio: string | number | null = null;
	/** 'contain' shows the whole image (no cropping). Use 'cover' for edge-to-edge fill. */
	export let fit: 'contain' | 'cover' | 'fill' | 'none' | 'scale-down' = 'contain';
	/** rounded corners and subtle shadow toggles */
	export let rounded = true;
	export let shadow = false;

	/** Optional responsive props */
	export let sizes: string | undefined = undefined;
	export let width: number | undefined = undefined;
	export let height: number | undefined = undefined;

	/** Optional <picture> sources for AVIF/WebP, etc. */
	export let sources: Array<{ srcset: string; type?: string; media?: string }> = [];

	/** Prefix SvelteKit base path for absolute `/...` URLs */
	export let useBase = true;

	let resolvedSrc: string | undefined;
	let objectUrl: string | undefined;

	const isHttp = (u: string) => /^https?:\/\//i.test(u);
	const needsBase = (u: string) => useBase && u.startsWith('/') && !isHttp(u);

	function computeSrc(s: typeof src): string | undefined {
		if (typeof s === 'string') {
			return needsBase(s) ? `${base}${s}` : s;
		}
		// File/Blob handled in onMount
		return objectUrl;
	}

	$: resolvedSrc = computeSrc(src);

	onMount(() => {
		if (src instanceof File || src instanceof Blob) {
			objectUrl = URL.createObjectURL(src);
			resolvedSrc = objectUrl;
		}
	});
	onDestroy(() => {
		if (objectUrl) URL.revokeObjectURL(objectUrl);
	});

	$: hasRatio = ratio !== null && `${ratio}`.trim() !== '';
	$: frameStyle = hasRatio ? `aspect-ratio:${ratio};` : undefined;

	function onImgError(e: Event) {
		const el = e.currentTarget as HTMLImageElement;
		// Optional: visually mark a broken image but don’t crash
		el.style.opacity = '0.3';
	}
</script>

<figure class="eimg">
	<div
		class="frame {rounded ? 'rounded' : ''} {shadow ? 'shadow' : ''} {hasRatio ? 'has-ratio' : ''}"
		style={frameStyle}
	>
		{#if sources.length}
			<picture>
				{#each sources as s, i (s.srcset + (s.media ?? '') + (s.type ?? ''))}
					<source srcset={s.srcset} media={s.media} type={s.type} {sizes} />
				{/each}
				{#if resolvedSrc}
					<img
						src={resolvedSrc}
						{alt}
						{sizes}
						{width}
						{height}
						loading="lazy"
						decoding="async"
						style={`object-fit:${fit};`}
						on:error={onImgError}
						aria-hidden={alt === '' ? 'true' : undefined}
					/>
				{/if}
			</picture>
		{:else if resolvedSrc}
			<img
				src={resolvedSrc}
				{alt}
				{sizes}
				{width}
				{height}
				loading="lazy"
				decoding="async"
				style={`object-fit:${fit};`}
				on:error={onImgError}
				aria-hidden={alt === '' ? 'true' : undefined}
			/>
		{/if}
	</div>
	<slot name="caption" />
</figure>

<style>
	.eimg {
		margin: 0;
	}

	.frame {
		position: relative;
		width: 100%;
		background: var(--white-background, #fff);
		overflow: hidden;
	}
	.frame.rounded {
		border-radius: var(--radius, 14px);
	}
	.frame.shadow {
		box-shadow: 0 10px 24px rgba(0, 0, 0, 0.08);
	}

	/* Natural height when no ratio is applied (no crop) */
	img {
		display: block;
		width: 100%;
		height: auto;
	}

	/* If aspect-ratio is set on frame, fill it (object-fit controls crop/letterbox) */
	.frame.has-ratio img {
		height: 100%;
	}

	@media (prefers-reduced-motion: reduce) {
		img {
			transition: none;
		}
	}
</style>
