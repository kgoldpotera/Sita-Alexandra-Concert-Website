<script lang="ts">
	// Allow overrides if you ever need them
	export let href = 'https://tezlow.com/SitaAlexandra';
	export let title = 'Join the SitaAlexandra Fan Club';
	export let blurb =
		'Be first in line for presale tickets, new drops, behind-the-scenes moments, and members-only surprises. It’s free to join — come be part of the family.';
</script>

<section class="fanclub">
	<a
		class="card"
		{href}
		target="_blank"
		rel="noopener noreferrer"
		aria-label="Join the Sita Alexandra Fan Club"
	>
		<div class="eyebrow">Exclusive Access</div>
		<h3 class="heading">{title}</h3>
		<p class="copy">{blurb}</p>
		<span class="cta">Join now →</span>
	</a>
</section>

<style>
	/* Palette from your artwork */
	:root {
		--madder: #a60530; /* primary red */
		--sunset: #f2c879; /* warm gold */
		--bistre: #2b1c14; /* deep brown */
	}

	.fanclub {
		width: 100%;
		padding-inline: clamp(12px, 3.5vw, 28px);
		/* prevent any inner element from causing x-scroll on small screens */
		overflow-x: hidden;
	}

	.card {
		/* Layout */
		display: block;
		width: 85%;
		max-width: 980px; /* was width: min(100%, 980px) */
		margin-inline: auto;
		padding: clamp(18px, 3.8vw, 36px);

		/* Premium surface */
		border-radius: 22px;
		background: linear-gradient(180deg, rgba(242, 200, 121, 0.12), rgba(166, 5, 48, 0.08)), #fff;
		border: 1px solid color-mix(in oklab, var(--madder) 28%, #ffffff);
		box-shadow:
			0 10px 30px rgba(166, 5, 48, 0.12),
			0 2px 8px rgba(0, 0, 0, 0.06);

		/* Type & link */
		color: var(--bistre);
		text-decoration: none;

		/* Motion */
		transition:
			transform 0.28s ease,
			box-shadow 0.28s ease,
			border-color 0.28s ease;
		position: relative;

		box-sizing: border-box;
		overflow: clip; /* clip glow to the card box */
	}

	/* Subtle gradient ring — fully inside the card so it can't overflow */
	.card::before {
		content: '';
		position: absolute;
		inset: 0; /* was -1px; caused overflow on narrow screens */
		border-radius: inherit;
		pointer-events: none;
		background: linear-gradient(135deg, var(--sunset), var(--madder));
		opacity: 0.3;
		padding: 1px; /* creates a 1px inner ring */
		mask:
			linear-gradient(#000 0 0) content-box,
			linear-gradient(#000 0 0);
		-webkit-mask:
			linear-gradient(#000 0 0) content-box,
			linear-gradient(#000 0 0);
	}

	/* Fallback if overflow: clip is unsupported */
	@supports not (overflow: clip) {
		.card {
			overflow: hidden;
		}
	}

	.card:hover {
		transform: translateY(-2px);
		box-shadow:
			0 18px 44px rgba(166, 5, 48, 0.18),
			0 4px 14px rgba(0, 0, 0, 0.08);
		border-color: color-mix(in oklab, var(--madder) 40%, #ffffff);
	}

	.card:focus-visible {
		outline: 3px solid color-mix(in oklab, var(--sunset) 60%, #ffffff);
		outline-offset: 3px;
	}

	.eyebrow {
		font-size: clamp(0.72rem, 0.9vw, 0.85rem);
		text-transform: uppercase;
		letter-spacing: 0.14em;
		font-weight: 700;
		color: color-mix(in oklab, var(--madder) 72%, #000);
		margin-bottom: 0.4rem;
	}

	.heading {
		font-size: clamp(1.25rem, 2.4vw, 2rem);
		line-height: 1.15;
		margin: 0 0 0.6rem 0;
		font-weight: 800;
		color: var(--bistre);
	}

	.copy {
		margin: 0 0 1.1rem 0;
		font-size: clamp(0.95rem, 1.3vw, 1.05rem);
		line-height: 1.55;
		color: color-mix(in oklab, var(--bistre) 82%, #000);
		max-width: 65ch;
	}

	.cta {
		display: inline-flex;
		align-items: center;
		gap: 0.5rem;
		padding: 0.7rem 1.05rem;
		border-radius: 999px;
		background: linear-gradient(90deg, var(--madder), color-mix(in oklab, var(--madder) 70%, #000));
		font-weight: 700;
		letter-spacing: 0.2px;
		box-shadow: 0 10px 22px rgba(166, 5, 48, 0.35);
		transition: transform 0.25s ease;
	}

	.card:hover .cta {
		transform: translateX(2px);
	}
</style>
