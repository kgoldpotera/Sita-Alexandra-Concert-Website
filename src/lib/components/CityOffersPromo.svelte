<script lang="ts">
	export let titleLine1 = 'Heading to a city to watch the shows?';
	export let titleLine2 = 'Explore offers from partners around the world';
</script>

<section class="section-head" aria-label="Partner offers heading">
	<h2 class="heading">
		<span class="line line-2">{titleLine1}</span>
		<span class="line line-2">{titleLine2}</span>
	</h2>
</section>

<style>
	.section-head {
		--sunset: var(--sunset, #f2c879);
		--madder: var(--madder, #a60530);
		--bistre: var(--bistre, #2b1c14);
		width: 100%;
		margin: 16px 0 6px; /* a touch tighter */
	}

	.heading {
		display: inline-block; /* underline hugs text width */
		margin: 0;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 900;
		letter-spacing: -0.02em;
		line-height: 1.08;
		text-wrap: balance;
		color: var(--text-color, #1b1b1b);
		font-size: clamp(24px, 6vw, 46px);
		text-align: center; /* mobile */
		margin-inline: auto;
		position: relative;
		padding-bottom: 6px; /* room for hairline underline */
	}

	/* Thin 2px underline in sunset */
	.heading::after {
		content: '';
		position: absolute;
		left: 0;
		right: 0;
		bottom: 0;
		height: 2px;
		border-radius: 2px;
		background: color-mix(in srgb, var(--sunset) 85%, #fff 15%);
	}

	.line {
		display: block;
	}

	/* Accent style — SOLID color (no gradient, never disappears) */
	.line-2 {
		color: var(--madder);
	}

	@media (min-width: 900px) {
		.heading {
			text-align: left;
			margin-inline: 0;
		}
	}

	/* Dark theme underline contrast */
	:root[data-theme='dark'] .section-head .heading::after {
		background: color-mix(in srgb, var(--sunset) 65%, var(--bistre) 35%);
	}
</style>
