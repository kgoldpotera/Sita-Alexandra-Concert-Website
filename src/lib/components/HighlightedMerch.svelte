<script lang="ts">
	// Change the title if you prefer: "Featured Drops" / "Editor’s Picks" / "Tour Highlights"
	export let title = 'Highlighted merchandise';

	// Global link (used if an item has no custom href)
	export let merchUrl = 'https://homebrandsitaalexandra.com/';

	// Provide exactly 4 items (you can pass your own from the page)
	export let items: Array<{
		src: string;
		alt?: string;
		label?: string;
		href?: string;
	}> = [
		{ src: '/highlight/one.jpg', label: 'Arena Night Tee' },
		{ src: '/highlight/two.jpg', label: 'Championship Jersey' },
		{ src: '/highlight/three.jpg', label: 'Tour Poster' },
		{ src: '/highlight/four.jpg', label: 'Collector Cap' }
	];

	// Fallback for missing images
	function onImgError(e: Event) {
		const img = e.currentTarget as HTMLImageElement;
		if (!img.src.includes('placeholder.png')) img.src = '/merch/placeholder.png';
	}
</script>

<section class="highlighted">
	<h2 class="heading">{title}</h2>

	<div class="grid">
		{#each items as it (it.src)}
			<a
				class="card"
				href={it.href ?? merchUrl}
				target="_blank"
				rel="noopener noreferrer"
				aria-label={`Open ${it.label ?? 'merch item'} on merchandise site`}
			>
				<div class="imgbox">
					<!-- Show the full image (never cropped) -->
					<img
						class="thumb"
						src={it.src}
						alt={it.alt ?? it.label ?? 'Merch item'}
						loading="lazy"
						decoding="async"
						on:error={onImgError}
					/>
				</div>
				{#if it.label}
					<div class="meta">{it.label}</div>
				{/if}
			</a>
		{/each}
	</div>
</section>

<style>
	.highlighted {
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

	/* Grid: 2 columns on mobile, 4 on desktop */
	.grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 14px;
	}
	@media (min-width: 900px) {
		.grid {
			grid-template-columns: repeat(4, 1fr);
			gap: 16px;
		}
	}

	/* Card */
	.card {
		display: grid;
		gap: 10px;
		color: inherit;
		text-decoration: none;
	}

	/* Square frame; image fully visible (no cropping) */
	.imgbox {
		position: relative;
		width: 100%;
		aspect-ratio: 1 / 1;
		border-radius: 14px;
		background: var(--white-background);
		border: 1px solid var(--white-border);
		overflow: hidden;

		/* center content so any letterboxing is even */
		display: flex;
		align-items: center;
		justify-content: center;

		transition:
			box-shadow 0.18s ease,
			transform 0.18s ease;
	}

	.thumb {
		/* key part: don't force both dimensions to 100% */
		width: auto;
		height: auto;
		max-width: 100%;
		max-height: 100%;
		object-fit: contain;
		object-position: center;
		display: block;
		padding: 6px; /* optional breathing room */
	}

	.card:hover .imgbox {
		transform: translateY(-1px);
		box-shadow: 0 6px 18px rgba(0, 0, 0, 0.12);
	}

	.meta {
		text-align: center;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 600;
		font-size: clamp(14px, 3.2vw, 16px);
	}
</style>
