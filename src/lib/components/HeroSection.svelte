<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	export let slides = [
		{
			src: '/hero-1.jpg',
			title: 'Welcome to the Championship StadiuM World Tour',
			desc: 'Experience electrifying nights across the globe—massive stages, immersive visuals, and unforgettable crowd energy.',
			ctaHref: 'https://homebrandsitaalexandra.com/',
			ctaLabel: 'Explore more'
		},
		{
			src: '/hero-2.jpg',
			title: 'The championship stadium concert tour',
			desc: 'A city-to-city spectacle featuring headline performances, surprise guests, and stadium-scale sound.',
			ctaHref: 'https://homebrandsitaalexandra.com/',
			ctaLabel: 'Explore more'
		},
		{
			src: '/hero-3.jpg',
			title: 'Stadium world tour',
			desc: 'The world’s biggest arenas unite for one historic run.',
			ctaHref: 'https://homebrandsitaalexandra.com/',
			ctaLabel: 'Explore more'
		}
	];

	let current = 0;
	const intervalMs = 9000;
	let timer: number | undefined;

	const go = (i: number) => (current = (i + slides.length) % slides.length);
	const next = () => go(current + 1);
	const prev = () => go(current - 1);

	const start = () => (timer = window.setInterval(next, intervalMs));
	const stop = () => timer && window.clearInterval(timer);

	// swipe for mobile
	let startX = 0;
	const onTouchStart = (e: TouchEvent) => {
		startX = e.touches[0].clientX;
		stop();
	};
	const onTouchEnd = (e: TouchEvent) => {
		const dx = e.changedTouches[0].clientX - startX;
		if (Math.abs(dx) > 40) dx < 0 ? next() : prev();
		start();
	};

	onMount(() => {
		start();
		const keyHandler = (e: KeyboardEvent) => {
			if (e.key === 'ArrowRight') {
				stop();
				next();
				start();
			}
			if (e.key === 'ArrowLeft') {
				stop();
				prev();
				start();
			}
		};
		window.addEventListener('keydown', keyHandler);
		return () => {
			stop();
			window.removeEventListener('keydown', keyHandler);
		};
	});
	onDestroy(stop);
</script>

<section
	class="hero"
	role="region"
	aria-roledescription="carousel"
	aria-label="Featured hero"
	on:touchstart={onTouchStart}
	on:touchend={onTouchEnd}
>
	{#each slides as slide, i}
		<article
			class="slide {i === current ? 'active' : ''}"
			aria-hidden={i === current ? 'false' : 'true'}
			aria-label={`Slide ${i + 1} of ${slides.length}`}
		>
			<img class="media" src={slide.src} alt="" />
			<div class="shade"></div>

			<div class="copy">
				<h1 class="headline">{slide.title}</h1>
				<p class="desc">{slide.desc}</p>
				<a class="cta" href={slide.ctaHref} target="_blank" rel="noopener">
					{slide.ctaLabel ?? 'Explore more'}
				</a>
			</div>
		</article>
	{/each}

	<button
		class="nav prev"
		aria-label="Previous slide"
		on:click={() => {
			stop();
			prev();
			start();
		}}>‹</button
	>

	<button
		class="nav next"
		aria-label="Next slide"
		on:click={() => {
			stop();
			next();
			start();
		}}>›</button
	>

	<div class="dots" role="tablist" aria-label="Select slide">
		{#each slides as _, i}
			<button
				class="dot {i === current ? 'active' : ''}"
				role="tab"
				aria-selected={i === current}
				aria-label={`Go to slide ${i + 1}`}
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
	/* HERO — no top gap, rounded corners, no horizontal overflow */
	.hero {
		--fit: contain;
		--kb-dur: 9s;
		position: relative;

		/* ✅ no horizontal scroll + no top margin */
		inline-size: 100%;
		margin: 0 !important; /* remove any top margin */

		/* ✅ rounded corners (clips media/arrows via overflow) */

		overflow: hidden;

		/* sizing */
		height: min(82svh, 720px);
		min-height: 480px;

		background: #000;
		color: #fff;
		isolation: isolate;
		border: 0;
	}

	/* Slides */
	.slide {
		position: absolute;
		inset: 0;
		opacity: 0;
		transition: opacity 0.6s ease;
	}
	.slide.active {
		opacity: 1;
	}

	/* Media + Ken Burns */
	.media {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
		object-fit: var(--fit);
		object-position: center;
		transform: scale(1.05);
		/* no per-image radius needed; parent clips it */
	}
	.slide.active .media {
		animation: kenburns var(--kb-dur) ease-out forwards;
	}
	@keyframes kenburns {
		from {
			transform: scale(1.05);
		}
		to {
			transform: scale(1.18);
		}
	}

	/* Shade */
	.shade {
		position: absolute;
		inset: 0;
		background: linear-gradient(
			90deg,
			rgba(0, 0, 0, 0.86) 0%,
			rgba(0, 0, 0, 0.55) 46%,
			rgba(0, 0, 0, 0.2) 72%,
			rgba(0, 0, 0, 0) 100%
		);
		z-index: 1;
	}

	/* Copy */
	.copy {
		position: absolute;
		left: 16px;
		right: 16px;
		bottom: calc(16px + env(safe-area-inset-bottom, 8px));
		z-index: 2;
		max-width: 38ch;
		animation: rise 0.6s ease both;
	}
	@keyframes rise {
		from {
			opacity: 0;
			transform: translateY(8px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}

	/* Type */
	.headline {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 800;
		line-height: 1.08;
		letter-spacing: -0.015em;
		margin: 0 0 10px;
		text-wrap: balance;
		font-size: clamp(26px, 6.4vw, 52px);
	}
	.desc {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 500;
		line-height: 1.55;
		color: #f1f1f1;
		margin: 0 0 16px;
		font-size: clamp(15px, 3.9vw, 20px);
	}

	/* CTA */
	.cta {
		display: inline-block;
		border: 1.5px solid #fff;
		padding: 12px 16px;
		border-radius: 14px;
		font-weight: 600;
		text-decoration: none;
		color: #0e0f11;
		background: #fff;
		transition:
			transform 0.15s ease,
			opacity 0.15s ease;
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
	}
	.cta:hover {
		opacity: 0.95;
		transform: translateY(-1px);
	}

	/* Arrows */
	.nav {
		position: absolute;
		top: 50%;
		translate: 0 -50%;
		z-index: 3;
		width: 38px;
		height: 38px;
		border-radius: 999px;
		border: 1px solid rgba(255, 255, 255, 0.55);
		background: rgba(0, 0, 0, 0.25);
		color: #fff;
		display: grid;
		place-items: center;
		cursor: pointer;
	}
	.prev {
		left: 10px;
	}
	.next {
		right: 10px;
	}

	/* Dots */
	.dots {
		position: absolute;
		left: 50%;
		bottom: calc(12px + env(safe-area-inset-bottom, 6px));
		translate: -50% 0;
		display: flex;
		gap: 8px;
		z-index: 3;
	}
	.dot {
		width: 9px;
		height: 9px;
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.55);
		border: none;
		cursor: pointer;
	}
	.dot.active {
		background: #fff;
		width: 20px;
		border-radius: 999px;
		transition: width 0.25s ease;
	}

	/* Desktop */
	@media (min-width: 900px) {
		.hero {
			--fit: cover;
			height: min(86svh, 820px);
		}
		.copy {
			left: 6vw;
			right: auto;
			bottom: 10vh;
			max-width: 56ch;
		}
		.headline {
			font-size: clamp(44px, 5.6vw, 72px);
		}
		.desc {
			font-size: clamp(17px, 1.9vw, 22px);
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
		.copy {
			animation: none;
		}
	}

	/* Safety net (page-wide) */
	:global(html, body) {
		margin: 0;
		padding: 0;
		overflow-x: hidden;
	}
</style>
