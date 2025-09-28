<!-- src/lib/components/PartnersGrid.svelte -->
<script lang="ts">
	import { base } from '$app/paths';

	type PartnerKey = 'hotels' | 'restaurants' | 'flights' | 'brands' | 'booths';
	type PartnerCard = {
		key: PartnerKey;
		title: string;
		blurb: string;
		image: string;
		href?: string;
	};

	// Static (no i18n)
	const exploreTxt = 'Explore';
	const headingTxt = 'Official Partners & Services';

	export let cards: PartnerCard[] = [
		{
			key: 'hotels',
			title: 'Hotels',
			blurb: 'Hand-picked stays near the venue—quiet, modern, and vetted for comfort.',
			image: '/partners/hotel.jpeg',
			href: '/partners/hotels'
		},
		{
			key: 'restaurants',
			title: 'Restaurants',
			blurb: 'Reservation-worthy tables from brunch to late supper. Curated with locals.',
			image: '/partners/restaurants.jpg',
			href: '/partners/hotels'
		},
		{
			key: 'flights',
			title: 'Flights',
			blurb: 'Trusted carriers and smart routes for smooth arrivals across cities.',
			image: '/partners/flights.jpeg',
			href: '/partners/flights'
		},
		{
			key: 'booths',
			title: 'Booths at Venue • T-Shirts • Posters • Games',
			blurb: 'Showcase your brand on the tour. Premium booth footprints with concierge support.',
			image: '/partners/booths.jpg',
			href: '/sponsor'
		}
	];

	// Resolve internal links with SvelteKit base
	const resolveHref = (href?: string) =>
		!href
			? undefined
			: /^https?:\/\//i.test(href) || href.startsWith('mailto:')
				? href
				: `${base}${href}`;
</script>

<section class="wide container svc">
	<h3 class="sr-only">{headingTxt}</h3>

	<div class="svc-list">
		{#each cards as c, i (c.key)}
			<article class="svc-row {i % 2 === 1 ? 'is-rev' : ''}">
				<figure class="svc-media">
					<img src={c.image} alt={c.title} loading="lazy" decoding="async" />
				</figure>

				<div class="svc-copy">
					<h4 class="svc-title">
						<span class="title-ink">{c.title}</span>
					</h4>
					<p class="svc-blurb">{c.blurb}</p>

					{#if c.href}
						<a class="svc-link" href={resolveHref(c.href)} aria-label={`${exploreTxt} ${c.title}`}>
							{exploreTxt}
							<span class="arrow" aria-hidden="true">→</span>
						</a>
					{/if}
				</div>
			</article>
		{/each}
	</div>
</section>

<style>
	/* ===== Theme tokens (from the palette image) ===== */
	:root {
		--sunset: #f2c879; /* warm gold */
		--madder: #a60530; /* deep red */
		--bistre: #2b1c14; /* rich brown/black */
		--ink: #1b1b1b;
		--muted: #4d4d4d;

		--radius: 16px;
		--border: color-mix(in srgb, var(--bistre) 14%, transparent);
	}

	/* a11y heading */
	.sr-only {
		position: absolute !important;
		width: 1px;
		height: 1px;
		padding: 0;
		margin: -1px;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border: 0;
	}

	/* ===== Section wrapper ===== */
	.svc {
		--gutter: clamp(8px, 4vw, 14px);
		margin-top: 10px;
		width: 100%;
		max-width: 100vw;
		overflow-x: hidden;
		padding-inline: var(--gutter);
		box-sizing: border-box;

		/* Subtle top-to-bottom warmth—kept restrained */
		background: linear-gradient(
			180deg,
			color-mix(in srgb, var(--sunset) 12%, transparent) 0%,
			transparent 22%
		);
		border-radius: 18px;
	}

	/* ===== Grid list ===== */
	.svc-list {
		display: grid;
		gap: clamp(16px, 2.6vw, 26px);
		min-width: 0;
	}

	/* Row: media + copy */
	.svc-row {
		display: grid;
		grid-template-columns: 1.08fr 1fr;
		gap: clamp(14px, 2.2vw, 22px);
		align-items: center;
		inline-size: 100%;
		max-inline-size: min(100%, calc(100vw - 2 * var(--gutter)));
		margin-inline: auto;
		min-width: 0;
	}
	.svc-row.is-rev .svc-media {
		order: 2;
	}
	.svc-row.is-rev .svc-copy {
		order: 1;
	}

	/* ===== Media card ===== */
	.svc-media {
		margin: 0;
		inline-size: 100%;
		max-inline-size: 100%;
		border-radius: var(--radius);
		border: 1px solid var(--border);
		overflow: hidden;
		background: #fff;
		box-shadow:
			0 14px 28px color-mix(in srgb, var(--bistre) 12%, transparent),
			0 1px 0 rgba(0, 0, 0, 0.02);
		aspect-ratio: 16 / 11;
	}
	.svc-media img {
		display: block;
		inline-size: 100%;
		block-size: 100%;
		object-fit: cover;
		object-position: center;
		border-radius: inherit;
		transition:
			transform 420ms ease,
			filter 300ms ease;
		will-change: transform, filter;
	}
	.svc-media:hover img {
		transform: scale(1.02);
		filter: saturate(1.03) contrast(1.03);
	}

	/* ===== Copy ===== */
	.svc-copy {
		align-self: center;
		min-width: 0;
	}

	.svc-title {
		margin: 0 0 8px;
		font-size: clamp(18px, 2.2vw, 20px);
		font-weight: 800;
		letter-spacing: -0.01em;
		color: var(--ink);
		line-height: 1.2;
	}

	/* Thin 2px underline in sunset (replaces the thicker wash) */
	.title-ink {
		position: relative;
		display: inline-block;
		padding-bottom: 2px; /* room for the hairline */
		background: none; /* remove previous background wash */
	}
	.title-ink::after {
		content: '';
		position: absolute;
		left: 0;
		right: 0;
		bottom: 0.1em; /* slight lift above descenders */
		height: 2px; /* <<< thinner underline */
		border-radius: 2px;
		background: color-mix(in srgb, var(--sunset) 85%, #fff 15%);
	}

	.svc-blurb {
		margin: 0 0 12px;
		line-height: 1.5;
	}

	/* ===== CTA (madder-forward, premium pill) ===== */
	.svc-link {
		--ring: color-mix(in srgb, var(--madder) 25%, transparent);
		display: inline-flex;
		align-items: center;
		gap: 8px;

		padding: 10px 14px;
		border-radius: 999px;
		font-weight: 800;
		text-decoration: none;
		letter-spacing: 0.1px;

		color: #fff;
		background: linear-gradient(
			180deg,
			color-mix(in srgb, var(--madder) 92%, #b40d3a 8%) 0%,
			color-mix(in srgb, var(--madder) 85%, var(--bistre) 15%) 100%
		);
		box-shadow:
			0 10px 26px color-mix(in srgb, var(--madder) 25%, transparent),
			inset 0 0 0 1px color-mix(in srgb, #ffffff 30%, transparent);
		border: none;
		transition:
			transform 0.15s ease,
			box-shadow 0.2s ease,
			filter 0.2s ease;
	}
	.svc-link .arrow {
		translate: 0 0;
		transition: translate 0.15s ease;
	}
	.svc-link:hover {
		transform: translateY(-1px);
		box-shadow:
			0 16px 34px color-mix(in srgb, var(--madder) 32%, transparent),
			inset 0 0 0 1px color-mix(in srgb, #ffffff 38%, transparent);
		filter: saturate(1.02);
	}
	.svc-link:hover .arrow {
		translate: 1px 0;
	}
	.svc-link:focus-visible {
		outline: none;
		box-shadow:
			0 0 0 4px var(--ring),
			0 12px 30px color-mix(in srgb, var(--madder) 28%, transparent);
	}

	/* ===== Mobile (≤860px) ===== */
	@media (max-width: 860px) {
		.svc-row {
			grid-template-columns: 1fr;
			gap: clamp(10px, 3vw, 16px);
			max-inline-size: calc(100vw - 2 * var(--gutter));
			margin-inline: auto;
		}
		.svc-row.is-rev .svc-media,
		.svc-row.is-rev .svc-copy {
			order: initial;
		}

		.svc-media {
			border-radius: calc(var(--radius) + 6px);
			aspect-ratio: 4 / 3;
		}
		.svc-copy {
			padding-inline: 2px;
		}
		.svc-title {
			font-size: clamp(16px, 4.8vw, 20px);
			line-height: 1.15;
		}
		.svc-blurb {
			font-size: clamp(13px, 3.6vw, 15px);
		}

		.svc-link {
			inline-size: 100%;
			justify-content: center;
			box-sizing: border-box;
		}
	}

	/* ===== Dark mode ===== */
	:root[data-theme='dark'] .svc {
		background: linear-gradient(
			180deg,
			color-mix(in srgb, var(--bistre) 75%, transparent) 0%,
			transparent 26%
		);
	}
	:root[data-theme='dark'] .svc-media {
		border-color: color-mix(in srgb, #fff 10%, transparent);
		background: color-mix(in srgb, #000 40%, transparent);
		box-shadow: none;
	}
	:root[data-theme='dark'] .svc-title {
		color: #fff;
	}
	:root[data-theme='dark'] .svc-blurb {
		color: color-mix(in srgb, #fff 70%, transparent);
	}

	/* dark-mode underline tone */
	:root[data-theme='dark'] .title-ink::after {
		background: color-mix(in srgb, var(--sunset) 65%, var(--bistre) 35%);
	}

	:root[data-theme='dark'] .svc-link {
		box-shadow:
			0 10px 26px color-mix(in srgb, var(--madder) 35%, transparent),
			inset 0 0 0 1px color-mix(in srgb, #ffffff 22%, transparent);
	}
	:root[data-theme='dark'] .svc-link:focus-visible {
		box-shadow:
			0 0 0 4px color-mix(in srgb, var(--madder) 40%, transparent),
			0 12px 30px color-mix(in srgb, var(--madder) 30%, transparent);
	}
</style>
