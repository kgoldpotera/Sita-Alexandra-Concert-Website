<script lang="ts">
	import { resolve } from '$app/paths';
	import ElegantImage from '$lib/components/ElegantImage.svelte';

	/** Page to link to when logos are clickable (internal or external) */
	export let sponsorsHref: string = '/sponsors';

	/** Logos behave as links when true */
	export let linkLogos = false;

	/** Your logos */
	export let logos: Array<{ src: string; alt?: string }> = [
		{ src: '/logos/Lamptafm.png', alt: 'LamptaFM' },
		{ src: '/logos/Tezlow.jpg', alt: 'Tezlow' }
	];

	/** Width cap for the section */
	export let maxWidth = '1080px';

	const isExternal = (u: string) => /^(https?:\/\/|mailto:|tel:)/i.test(u);

	// Use resolve() for internal paths (cast to any to satisfy TS route union),
	// and raw URL for externals. This keeps ESLint happy without svelte-ignore.
	$: hrefResolved = isExternal(sponsorsHref) ? sponsorsHref : resolve(sponsorsHref as any);

	function onImgError(e: Event) {
		const img = e.currentTarget as HTMLImageElement;
		img.decoding = 'async';
		img.src = '/logos/placeholder.png';
	}
</script>

<section
	class="sponsors is-centered"
	style={`--sponsors-max:${maxWidth}`}
	aria-label="Partners & sponsors"
>
	<h3 class="section-title sponsors__title">
		<span class="title-link">Our Partners · Sponsors</span>
	</h3>

	<div class="logo-grid" role="list">
		{#each logos as logo, i (logo.src || i)}
			{#if linkLogos}
				<!-- non-interactive listitem wrapper for a11y -->
				<div class="logo-item" role="listitem" aria-label={logo.alt ?? 'Partner logo'}>
					<a
						class="logo-card"
						href={hrefResolved}
						target={isExternal(hrefResolved) ? '_blank' : undefined}
						rel={isExternal(hrefResolved) ? 'noopener noreferrer' : undefined}
						aria-label={logo.alt ?? 'Partner logo'}
					>
						<img
							class="brand-logo"
							{...{ src: logo.src, alt: logo.alt ?? '' }}
							loading="lazy"
							decoding="async"
							on:error={onImgError}
						/>
					</a>
				</div>
			{:else}
				<div class="logo-card" role="listitem" aria-label={logo.alt ?? 'Partner logo'}>
					<img
						class="brand-logo"
						{...{ src: logo.src, alt: logo.alt ?? '' }}
						loading="lazy"
						decoding="async"
						on:error={onImgError}
					/>
				</div>
			{/if}
		{/each}
	</div>
</section>

<ElegantImage src="/poster.jpeg" alt="SitaAlexandra Poster" fit="contain" />

<style>
	.sponsors,
	.sponsors * {
		box-sizing: border-box;
		min-width: 0;
	}

	.sponsors {
		inline-size: 100%;
		max-inline-size: min(100%, var(--sponsors-max));
		margin-inline: auto;
		overflow-x: hidden;
	}

	.sponsors.is-centered {
		grid-column: 1 / -1;
		justify-self: center;
		align-self: center;
	}

	.sponsors__title {
		margin: 0 0 12px;
	}

	.title-link {
		color: inherit;
		text-decoration: none;
		border-radius: 999px;
		padding: 2px 6px;
		text-underline-offset: 4px;
	}

	.logo-grid {
		display: grid;
		grid-template-columns: repeat(2, minmax(160px, 1fr));
		gap: clamp(12px, 2.8vw, 24px);
		align-items: center;
		justify-items: center;
		padding: clamp(8px, 2.4vw, 16px);
	}
	@media (max-width: 560px) {
		.logo-grid {
			grid-template-columns: 1fr;
		}
	}

	.logo-item {
		display: contents;
	}

	.logo-card {
		display: grid;
		place-items: center;
		inline-size: min(100%, clamp(220px, 44vw, 520px));
		aspect-ratio: 16 / 9;
		border-radius: 22px;
		background: #fff;
		border: 1px solid var(--border);
		box-shadow: 0 8px 22px rgba(0, 0, 0, 0.06);
		padding: clamp(12px, 3vw, 20px);
		text-decoration: none;
		transition:
			transform 0.15s ease,
			box-shadow 0.15s ease,
			border-color 0.15s ease,
			filter 0.2s ease;
		filter: saturate(0.9) contrast(1.02);
	}
	.logo-card:hover {
		transform: translateY(-1px);
		box-shadow: 0 12px 28px rgba(0, 0, 0, 0.1);
		border-color: var(--white-hover-border, #e5e7eb);
		filter: none;
	}
	.logo-card:focus-visible {
		outline: none;
		box-shadow: 0 0 0 4px color-mix(in srgb, var(--accent) 20%, transparent);
	}

	.brand-logo {
		display: block;
		inline-size: 100%;
		block-size: 100%;
		object-fit: contain;
		image-rendering: -webkit-optimize-contrast;
	}

	@media (max-width: 560px) {
		.logo-card {
			border-radius: 18px;
			inline-size: min(100%, clamp(200px, 86vw, 480px));
		}
	}

	:root[data-theme='dark'] .logo-card {
		background: color-mix(in srgb, #000 40%, transparent);
		border-color: color-mix(in srgb, #fff 12%, transparent);
		box-shadow: 0 10px 28px rgba(0, 0, 0, 0.3);
	}
</style>
