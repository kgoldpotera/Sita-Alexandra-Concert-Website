<!-- src/routes/hotels/+page.svelte (no i18n, no header/footer) -->
<script lang="ts">
	import { onMount } from 'svelte';
	import HeroTopbar from '$lib/components/HeroTopbar.svelte';

	// Static copy
	const headingTxt = 'Hotels & Restaurants across UK Cities';
	const jumpToCityTxt = 'Jump to city';
	const goToTxt = 'Go to';

	type CityIntroKey = 'london' | 'edinburgh' | 'manchester' | 'liverpool';
	const intro: Record<CityIntroKey, string> = {
		london: 'Classic, cutting-edge, and culinary icons.',
		edinburgh: 'Stone closes, skyline spires, and warm Scottish hospitality.',
		manchester: 'Industrial grandeur meets modern dining culture.',
		liverpool: 'Waterfront charm and a flourishing food scene.'
	};

	type Place = { name: string; type: 'Hotel' | 'Restaurant'; url: string };
	type City = { name: string; image: string; introKey: CityIntroKey; places: Place[] };

	// Ensure these images exist in /static/cities/*
	const cities: City[] = [
		{
			name: 'London',
			image: '/cities/london.jpg',
			introKey: 'london',
			places: [
				{ name: 'The Ritz London', type: 'Hotel', url: 'https://www.theritzlondon.com/' },
				{
					name: 'The Dorchester',
					type: 'Hotel',
					url: 'https://www.dorchestercollection.com/en/london/the-dorchester/'
				},
				{
					name: 'Dishoom Covent Garden',
					type: 'Restaurant',
					url: 'https://www.dishoom.com/covent-garden/'
				}
			]
		},
		{
			name: 'Edinburgh',
			image: '/cities/edinburgh.jpg',
			introKey: 'edinburgh',
			places: [
				{
					name: 'The Balmoral',
					type: 'Hotel',
					url: 'https://www.roccofortehotels.com/hotels-and-resorts/the-balmoral-hotel/'
				},
				{
					name: 'Number One at The Balmoral',
					type: 'Restaurant',
					url: 'https://www.numberonerestaurant.com/'
				}
			]
		},
		{
			name: 'Manchester',
			image: '/cities/manchester.jpg',
			introKey: 'manchester',
			places: [
				{ name: 'The Midland', type: 'Hotel', url: 'https://www.themidlandmanchester.co.uk/' },
				{ name: '20 Stories', type: 'Restaurant', url: 'https://20stories.co.uk/' },
				{ name: 'The Lowry Hotel', type: 'Hotel', url: 'https://www.thelowryhotel.com/' }
			]
		},
		{
			name: 'Liverpool',
			image: '/cities/liverpool.jpg',
			introKey: 'liverpool',
			places: [
				{ name: 'Hope Street Hotel', type: 'Hotel', url: 'https://www.hopestreethotel.co.uk/' },
				{ name: 'Panoramic 34', type: 'Restaurant', url: 'https://www.panoramic34.com/' },
				{
					name: 'The Art School',
					type: 'Restaurant',
					url: 'https://www.theartschoolrestaurant.co.uk/'
				}
			]
		}
	];

	const slug = (s: string) =>
		s
			.toLowerCase()
			.replace(/\s+/g, '-')
			.replace(/[^a-z0-9-]/g, '');

	let activeIndex = 0;
	let parallax = 0;
	let sectionRefs: HTMLElement[] = [];

	function collectRef(node: HTMLElement, index: number) {
		sectionRefs[index] = node;
		return {
			destroy() {
				if (sectionRefs[index] === node) sectionRefs[index] = undefined as unknown as HTMLElement;
			}
		};
	}

	function scrollToIndex(i: number) {
		sectionRefs[i]?.scrollIntoView({ behavior: 'smooth', block: 'start' });
	}

	onMount(() => {
		const io = new IntersectionObserver(
			(entries) => {
				entries.forEach((e) => {
					if (e.isIntersecting) {
						const idx = sectionRefs.findIndex((n) => n === e.target);
						if (idx >= 0) activeIndex = idx;
						(e.target as HTMLElement).classList.add('visible');
					}
				});
			},
			{ threshold: 0.35, rootMargin: '-15% 0px -15% 0px' }
		);

		sectionRefs.filter(Boolean).forEach((el) => io.observe(el!));
		sectionRefs[0]?.classList.add('visible');

		const onScroll = () => {
			if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;
			parallax = window.scrollY * 0.12;
		};
		window.addEventListener('scroll', onScroll, { passive: true });
		onScroll();

		return () => {
			io.disconnect();
			window.removeEventListener('scroll', onScroll);
		};
	});
</script>

<HeroTopbar />
<main class="story">
	<!-- Sticky background stack -->
	<div class="bg" aria-hidden="true">
		{#each cities as city, i (city.name)}
			<img
				src={city.image}
				alt=""
				loading="lazy"
				class="bg-image {activeIndex === i ? 'active' : ''}"
				style="transform: translate3d(0, {activeIndex === i ? parallax * -0.1 : 0}px, 0);"
			/>
		{/each}
		<div class="scrim"></div>
	</div>

	<h1 class="sr-only">{headingTxt}</h1>

	{#each cities as city, i (city.name)}
		<section
			id={slug(city.name)}
			class="chapter {i === 0 ? 'visible' : ''}"
			aria-labelledby={'heading-' + i}
			use:collectRef={i}
		>
			<div class="chapter-inner">
				<header class="chapter-header">
					<h2 id={'heading-' + i} class="city">{city.name}</h2>
					<p class="intro">{intro[city.introKey]}</p>
				</header>

				<ul class="places" role="list">
					{#each city.places as place (place.name)}
						<li class="place">
							<a class="place-name" href={place.url} target="_blank" rel="noopener noreferrer">
								{place.name}<span class="external" aria-hidden="true">↗</span>
							</a>
							<span class="dot" aria-hidden="true">•</span>
							<span class="meta">{place.type}</span>
						</li>
					{/each}
				</ul>
			</div>
		</section>
	{/each}

	<!-- Dot navigation -->
	<nav class="dots" aria-label={jumpToCityTxt}>
		{#each cities as city, i (city.name)}
			<button
				type="button"
				class="dot-btn"
				aria-current={activeIndex === i ? 'true' : undefined}
				aria-label={`${goToTxt} ${city.name}`}
				title={city.name}
				on:click={() => scrollToIndex(i)}
			/>
		{/each}
	</nav>
</main>

<style>
	:global(:root) {
		--glass-bg: rgba(12, 12, 12, 0.42);
		--glass-border: rgba(255, 255, 255, 0.12);
		--text: #f5f5f5;
		--muted: #d1d1d1;
		--shadow: 0 10px 30px rgba(0, 0, 0, 0.35);
	}

	/* Remove any external magnifier helpers */
	:global(.magnifier),
	:global(.magnifier-lens),
	:global(.hero-magnifier),
	:global([data-magnifier]),
	:global(.mag-ov) {
		display: none !important;
	}

	.story {
		position: relative;
		min-height: 100vh;
		color: var(--text);
		overflow-x: hidden;
		z-index: 1;
	}

	/* Sticky background */
	.bg {
		position: fixed;
		inset: 0;
		z-index: -2;
		overflow: hidden;
		background: #0b0b0b;
	}
	.bg-image {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
		object-fit: cover;
		opacity: 0;
		transform: translate3d(0, 0, 0);
		transition:
			opacity 700ms ease,
			transform 700ms ease;
		will-change: opacity, transform;
		filter: saturate(0.9) contrast(1.03) brightness(0.92);
	}
	.bg-image.active {
		opacity: 1;
	}

	.scrim {
		position: absolute;
		inset: 0;
		pointer-events: none;
		background:
			radial-gradient(
				120% 70% at 50% 120%,
				rgba(0, 0, 0, 0.55) 0%,
				rgba(0, 0, 0, 0.12) 60%,
				rgba(0, 0, 0, 0) 100%
			),
			linear-gradient(180deg, rgba(0, 0, 0, 0.5) 0%, rgba(0, 0, 0, 0.1) 35%, rgba(0, 0, 0, 0) 60%);
	}

	/* Chapters */
	.chapter {
		position: relative;
		z-index: 20;
		min-height: 100vh;
		display: grid;
		place-items: center;
		padding: 6rem 1rem;
	}
	.chapter-inner {
		position: relative;
		z-index: 25;
		width: min(100% - 2rem, 980px);
		margin: 0 auto;
		padding: clamp(1.25rem, 2.5vw, 1.75rem);
		border-radius: 20px;
		background: var(--glass-bg);
		border: 1px solid var(--glass-border);
		box-shadow: var(--shadow);
		backdrop-filter: blur(10px);
		-webkit-backdrop-filter: blur(10px);
		transform: translateY(20px);
		opacity: 0;
		transition:
			transform 700ms ease,
			opacity 700ms ease,
			background 300ms ease;
	}
	.chapter.visible .chapter-inner {
		transform: translateY(0);
		opacity: 1;
	}

	.chapter-header {
		margin-bottom: 1rem;
	}
	.city {
		margin: 0 0 0.25rem 0;
		font-weight: 600;
		letter-spacing: 0.2px;
		font-size: clamp(1.75rem, 3.2vw, 2.5rem);
		line-height: 1.15;
	}
	.intro {
		margin: 0;
		font-size: clamp(0.95rem, 1.5vw, 1.05rem);
		line-height: 1.5;
	}

	.places {
		margin: 1rem 0 0 0;
		padding: 0;
		list-style: none;
		display: grid;
		gap: 0.65rem;
	}
	.place {
		display: flex;
		align-items: baseline;
		gap: 0.6rem;
		flex-wrap: wrap;
		font-size: clamp(1rem, 1.6vw, 1.05rem);
	}
	.place-name {
		text-decoration: none;
		color: var(--text);
		border-bottom: 1px solid transparent;
		transition:
			border-color 200ms ease,
			opacity 200ms ease;
		line-height: 1.2;
	}
	.place-name:hover,
	.place-name:focus-visible {
		border-bottom-color: currentColor;
		outline: none;
	}
	.external {
		margin-left: 0.25rem;
		font-size: 0.9em;
		opacity: 0.7;
	}
	.dot {
		opacity: 0.35;
	}
	.meta {
		font-size: 0.95em;
		letter-spacing: 0.2px;
	}

	/* Dot nav */
	.dots {
		position: fixed;
		left: max(12px, calc(50% - 490px - 24px));
		top: 50%;
		transform: translateY(-50%);
		display: grid;
		gap: 10px;
		z-index: 30;
	}
	.dot-btn {
		width: 12px;
		height: 12px;
		border-radius: 999px;
		border: 1px solid rgba(255, 255, 255, 0.45);
		background: rgba(255, 255, 255, 0.15);
		transition:
			transform 200ms ease,
			background 200ms ease,
			border-color 200ms ease;
	}
	.dot-btn:hover,
	.dot-btn:focus-visible {
		transform: scale(1.15);
		background: rgba(255, 255, 255, 0.25);
		outline: none;
	}
	.dot-btn[aria-current='true'] {
		background: rgba(255, 255, 255, 0.85);
		border-color: rgba(255, 255, 255, 0.9);
	}

	@media (max-width: 820px) {
		.dots {
			left: 10px;
		}
	}

	.sr-only {
		position: absolute;
		width: 1px;
		height: 1px;
		padding: 0;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border: 0;
	}

	@media (prefers-reduced-motion: reduce) {
		.bg-image {
			transition: opacity 0.01ms;
			transform: none !important;
		}
		.chapter-inner {
			transition:
				opacity 0.01ms,
				background 300ms ease;
			transform: none !important;
		}
	}
</style>
