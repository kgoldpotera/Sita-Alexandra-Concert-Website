<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	export let title = 'Explore here Partnership Merchandise Collections';
	export let ctaHref = 'https://homebrandsitaalexandra.com/';
	export let ctaLabel = 'Explore more';

	// Replace with your 5 background images (ideal 1600px+)
	export let slides: { src: string; label: string; alt?: string }[] = [
		{ src: '/merch/t-shirts.jpg', label: 'Limited Edition Tees' },
		{ src: '/merch/sports-kits.jpg', label: 'Collaborative Jerseys' },
		{ src: '/highlight/three.jpg', label: 'Signed Posters' },
		{ src: '/merch/stickers.jpg', label: 'Tour Stickers' },
		{ src: '/merch/magazines.jpg', label: 'Collector Magazines' }

	];

	let current = 0;
	const intervalMs = 8500; // slower, elegant
	let timer: number | undefined;

	const go = (i: number) => (current = (i + slides.length) % slides.length);
	const next = () => go(current + 1);
	const prev = () => go(current - 1);

	const start = () => (timer = window.setInterval(next, intervalMs));
	const stop = () => timer && window.clearInterval(timer);

	// Swipe on mobile
	let startX = 0;
	function onTouchStart(e: TouchEvent) {
		startX = e.touches[0].clientX;
		stop();
	}
	function onTouchEnd(e: TouchEvent) {
		const dx = e.changedTouches[0].clientX - startX;
		if (Math.abs(dx) > 40) dx < 0 ? next() : prev();
		start();
	}

	onMount(() => {
		start();
		return () => stop();
	});
	onDestroy(stop);
</script>

<section
	class="promo"
	on:touchstart={onTouchStart}
	on:touchend={onTouchEnd}
	aria-label="Partnership merchandise collections"
>
	<!-- Background slides -->
	{#each slides as s, i}
		<figure
			class="slide {i === current ? 'active' : ''}"
			aria-hidden={i === current ? 'false' : 'true'}
		>
			<img class="media" src={s.src} alt={s.alt ?? s.label} loading="lazy" />
		</figure>
	{/each}

	<!-- Overlays -->
	<div class="veil"></div>

	<div class="copy">
		<h3 class="heading">{title}</h3>
		<p class="sub">{slides[current]?.label}</p>

		<div class="actions">
			<a class="cta" href={ctaHref} target="_blank" rel="noopener noreferrer">{ctaLabel}</a>
			<div class="controls" aria-hidden="false">
				<button
					class="nav"
					aria-label="Previous"
					on:click={() => {
						stop();
						prev();
						start();
					}}>‹</button
				>
				<button
					class="nav"
					aria-label="Next"
					on:click={() => {
						stop();
						next();
						start();
					}}>›</button
				>
			</div>
		</div>
	</div>

	<!-- Bars pagination -->
	<div class="pager" role="tablist" aria-label="Change background">
		{#each slides as _, i}
			<button
				class="bar {i === current ? 'active' : ''}"
				role="tab"
				aria-selected={i === current}
				aria-label={`Show ${slides[i].label}`}
				on:click={() => {
					stop();
					go(i);
					start();
				}}
			/>
		{/each}
	</div>
</section>

<style>
	/* Card-like promo, mobile-first */
	.promo {
		--ratio: 4/3; /* mobile shape */
		--fit: contain; /* don’t crop on phones */
		--kb-dur: 9s; /* ken burns duration */
		position: relative;
		width: 100%;
		border-radius: var(--radius);
		overflow: hidden;
		background: #000;
		isolation: isolate;
		aspect-ratio: var(--ratio);
	}

	/* Slides stack and crossfade */
	.slide {
		position: absolute;
		inset: 0;
		opacity: 0;
		transition: opacity 0.7s ease;
	}
	.slide.active {
		opacity: 1;
	}

	.media {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
		object-fit: var(--fit);
		object-position: center;
		transform: scale(1.02);
	}
	.slide.active .media {
		animation: kenburns var(--kb-dur) ease-out forwards;
	}
	@keyframes kenburns {
		from {
			transform: scale(1.02);
		}
		to {
			transform: scale(1.12);
		}
	}

	/* Readability veil */
	.veil {
		position: absolute;
		inset: 0;
		background:
			linear-gradient(
				0deg,
				rgba(0, 0, 0, 0.55) 0%,
				rgba(0, 0, 0, 0.25) 38%,
				rgba(0, 0, 0, 0.1) 65%,
				rgba(0, 0, 0, 0) 100%
			),
			linear-gradient(90deg, rgba(0, 0, 0, 0.65) 0%, rgba(0, 0, 0, 0.25) 45%, rgba(0, 0, 0, 0) 85%);
		z-index: 1;
	}

	.copy {
		position: absolute;
		inset: auto 14px 14px 14px; /* bottom padding on phones */
		z-index: 2;
		color: #fff;
		max-width: 36ch;
	}

	.eyebrow {
		display: inline-block;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 700;
		letter-spacing: 0.12em;
		text-transform: uppercase;
		font-size: 12px;
		opacity: 0.9;
		margin-bottom: 6px;
	}

	.heading {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 800;
		line-height: 1.1;
		letter-spacing: -0.015em;
		margin: 0 0 6px;
		font-size: clamp(18px, 4.8vw, 28px);
	}

	.sub {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 600;
		margin: 0 0 12px;
		color: #f1f1f1;
		font-size: clamp(14px, 3.8vw, 18px);
	}

	.actions {
		display: flex;
		align-items: center;
		gap: 10px;
	}
	.cta {
		display: inline-block;
		background: #fff;
		color: #111;
		border: 1px solid #fff;
		border-radius: 12px;
		padding: 10px 14px;
		font-weight: 600;
		text-decoration: none;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
	}

	.controls {
		display: flex;
		gap: 8px;
	}
	.nav {
		width: 32px;
		height: 32px;
		border-radius: 999px;
		border: 1px solid rgba(255, 255, 255, 0.6);
		background: rgba(0, 0, 0, 0.25);
		color: #fff;
		display: grid;
		place-items: center;
	}

	/* Bars pagination */
	.pager {
		position: absolute;
		inset: auto 0 10px 0;
		display: flex;
		justify-content: center;
		gap: 10px;
		z-index: 2;
	}
	.bar {
		width: 40px;
		height: 6px;
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.35);
		border: none;
		cursor: pointer;
	}
	.bar.active {
		background: var(--accent);
	}

	/* Tablet+ : wider card and cover fit like the reference */
	@media (min-width: 800px) {
		.promo {
			--ratio: 16/9;
			--fit: cover;
		}
		.copy {
			inset: auto auto 24px 24px;
			max-width: 42ch;
		}
		.heading {
			font-size: clamp(22px, 3.5vw, 34px);
		}
		.sub {
			font-size: clamp(15px, 2vw, 20px);
		}
		.pager {
			bottom: 14px;
		}
	}

	/* Reduced motion */
	@media (prefers-reduced-motion: reduce) {
		.slide {
			transition: none;
		}
		.media,
		.slide.active .media {
			animation: none;
			transform: none;
		}
	}
</style>
