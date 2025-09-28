<script lang="ts">
	// Swap this with your chosen crowd image path under /static
	export let imgSrc = '/limited/crowd.jpg';
	export let imgAlt = 'A crowd watching a concert at the Championship Stadium World Tour';

	export let title = 'Limited edition collection';
	export let blurb =
		'Numbered drops celebrating the Championship Stadium World Tour — premium fabrics, bold artwork, and pieces inspired by the energy of a packed arena.';
	export let ctaHref = 'https://homebrandsitaalexandra.com/';
	export let ctaLabel = 'Explore more';
</script>

<section class="limited">
	<h2 class="heading">{title}</h2>

	<article class="card">
		<figure class="mediaWrap">
			<!-- Full image, never cropped -->
			<img class="media" src={imgSrc} alt={imgAlt} />
		</figure>

		<div class="copy">
			<h3 class="subheading">Championship Crowd Edition</h3>
			<p class="desc">{blurb}</p>
			<a class="cta" href={ctaHref} target="_blank" rel="noopener noreferrer">{ctaLabel}</a>
		</div>
	</article>
</section>

<style>
	/* Section */
	.limited {
		width: 100%;
		margin: 28px 0;
	}

	.heading {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 800;
		text-align: center;
		margin: 0 0 16px;
		letter-spacing: -0.015em;
		font-size: clamp(20px, 4.8vw, 32px);
		color: var(--text-color);
	}

	/* Card container (mobile: stacked; desktop: split) */
	.card {
		display: grid;
		gap: 14px;
		background: var(--white-background);
		border: 1px solid var(--white-border);
		border-radius: var(--radius);
		overflow: hidden;
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.06);
	}

	/* Image wrapper keeps a pleasant ratio; image NEVER cropped */
	.mediaWrap {
		margin: 0;
		width: 100%;
		aspect-ratio: 4 / 3; /* mobile shape */
		background: var(--background-alt);
		display: grid;
		place-items: center;
		overflow: hidden;
	}
	.media {
		width: 100%;
		height: 100%;
		object-fit: contain; /* ✅ show the whole photo */
		padding: 10px; /* breathing room */
		display: block;
	}

	.copy {
		padding: 12px 14px 16px;
	}
	.subheading {
		margin: 0 0 6px;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 800;
		letter-spacing: -0.01em;
		font-size: clamp(18px, 4.2vw, 22px);
		color: var(--text-color);
	}
	.desc {
		margin: 0 0 12px;
		color: #6b7280;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 500;
		font-size: clamp(14px, 3.6vw, 16px);
		line-height: 1.55;
	}
	.cta {
		display: inline-block;
		padding: 10px 14px;
		border-radius: 12px;
		border: 1px solid var(--white-border);
		background: var(--white-background);
		font-weight: 600;
		text-decoration: none;
		color: var(--text-color);
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		transition:
			background-color 0.15s ease,
			border-color 0.15s ease,
			transform 0.02s;
	}
	.cta:hover {
		background: var(--white-hover-background);
		border-color: var(--white-hover-border);
	}
	.cta:active {
		transform: translateY(1px);
	}

	/* Tablet/desktop: split layout (image left, text right) and wide ratio */
	@media (min-width: 900px) {
		.card {
			grid-template-columns: 1.2fr 1fr;
			align-items: center;
		}
		.mediaWrap {
			aspect-ratio: 16 / 9;
		} /* wider feel like reference */
		.copy {
			padding: 18px 18px 18px 0;
		}
		.subheading {
			font-size: clamp(20px, 2.4vw, 26px);
		}
		.desc {
			font-size: clamp(15px, 1.6vw, 17px);
		}
	}
</style>
