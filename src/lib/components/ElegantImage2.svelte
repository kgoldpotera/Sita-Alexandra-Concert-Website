<!-- src/lib/components/ElegantImage.svelte -->
<script lang="ts">
	export let src: string;
	/** leave empty for decorative image (no visible text) */
	export let alt: string = '';
	/** e.g. '16/9', '4/3' or a number. If omitted, height auto-sizes. */
	export let ratio: string | number | null = null;
	/** 'contain' shows the whole image (no cropping). Set to 'cover' if you want edge-to-edge fill. */
	export let fit: 'contain' | 'cover' = 'contain';
	/** rounded corners and subtle shadow toggles */
	export let rounded: boolean = true;
	export let shadow: boolean = false;

	const loading = 'lazy' as const;
</script>

<figure class="eimg">
	<div
		class="frame {rounded ? 'rounded' : ''} {shadow ? 'shadow' : ''}"
		style={ratio ? `aspect-ratio:${ratio};` : ''}
	>
		<img
			{src}
			{alt}
			{loading}
			decoding="async"
			style={`object-fit:${fit};`}
			aria-hidden={alt === '' ? 'true' : undefined}
		/>
	</div>
</figure>

<style>
	.eimg {
		margin: 0;
	}

	.frame {
		position: relative;
		width: 100%;
		/* if no ratio is provided, height will follow the image’s intrinsic size */
		background: var(--white-background, #fff);
		overflow: hidden;
	}
	.frame.rounded {
		border-radius: var(--radius, 14px);
	}
	.frame.shadow {
		box-shadow: 0 10px 24px rgba(0, 0, 0, 0.08);
	}

	img {
		width: 100%;
		height: 100%;
		display: block;
		/* object-fit set via inline style from prop */
	}

	@media (prefers-reduced-motion: reduce) {
		img {
			transition: none;
		}
	}
</style>
