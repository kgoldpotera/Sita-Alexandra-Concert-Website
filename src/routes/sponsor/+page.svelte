<script lang="ts">
	import { onMount } from 'svelte';
	import HeroTopbar from '$lib/components/HeroTopbar.svelte';
	import { resolve } from '$app/paths';

	// ----- Static copy (no i18n) -----
	const heading = 'Partner With Us';
	const subtitle = 'Showcase your brand to thousands of fans across our tours and releases.';
	const ctaText = 'Become a Sponsor';
	const jumpTo = 'Jump to slide';
	const goTo = 'Go to';
	const benefits: [string, string, string] = [
		'Prime logo placement across digital & live touchpoints.',
		'On-stage shout-outs and inclusion in press materials.',
		'Custom activations with measurable campaign outcomes.'
	];
	const slideCaption = (n: number) => `Sponsorship opportunity ${n}`;

	// Slides: your images in /static/sponsor
	type Slide = { id: string; src: string };
	const slides: Slide[] = Array.from({ length: 16 }, (_, i) => {
		const n = i + 1;
		return { id: `slide-${n}`, src: `/sponsor/image${n}.jpeg` };
	});

	// Utilities
	const slug = (s: string) =>
		s
			.toLowerCase()
			.replace(/\s+/g, '-')
			.replace(/[^a-z0-9-]/g, '');

	let activeIndex = 0;
	let parallax = 0;

	// keep TS happy
	let sectionRefs: Array<HTMLElement | null> = [];

	function collectRef(node: HTMLElement, index: number) {
		sectionRefs[index] = node;
		return {
			destroy() {
				if (sectionRefs[index] === node) sectionRefs[index] = null;
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

<HeroTopbar position="sticky" />

<main class="story">
	<!-- Sticky background stack -->
	<div class="bg" aria-hidden="true">
		{#each slides as s, i (s.id)}
			<img
				src={s.src}
				alt=""
				loading="lazy"
				class="bg-image {activeIndex === i ? 'active' : ''}"
				style="transform: translate3d(0, {activeIndex === i ? parallax * -0.1 : 0}px, 0);"
			/>
		{/each}
		<div class="scrim"></div>
	</div>

	<h1 class="sr-only">{heading}</h1>

	{#each slides as s, i (s.id)}
		<section
			id={slug(s.id)}
			class="chapter {i === 0 ? 'visible' : ''}"
			aria-labelledby={'heading-' + i}
			use:collectRef={i}
		>
			<div class="chapter-inner">
				<header class="chapter-header">
					<h2 id={'heading-' + i} class="title">{heading}</h2>
					<p class="intro">{subtitle}</p>
				</header>

				<ul class="benefits" role="list">
					{#each benefits as b, bi (bi)}
						<li>{b}</li>
					{/each}
				</ul>

				<div class="cta-row">
					<!-- internal route MUST be resolved -->
					<a class="btn" href={resolve('/sponsor/apply')} aria-label={ctaText}>
						{ctaText}
					</a>
				</div>
			</div>
		</section>
	{/each}

	<!-- Dot navigation -->
	<nav class="dots" aria-label={jumpTo}>
		{#each slides as s, i (s.id)}
			<button
				type="button"
				class="dot-btn"
				aria-current={activeIndex === i ? 'true' : undefined}
				aria-label={`${goTo} ${slideCaption(i + 1)}`}
				title={slideCaption(i + 1)}
				on:click={() => scrollToIndex(i)}
			></button>
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

	/* Hide any 3rd-party magnifier overlays if present */
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
		filter: saturate(0.95) contrast(1.05) brightness(0.92);
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
		margin-bottom: 0.75rem;
	}
	.title {
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
		color: var(--muted);
	}

	.benefits {
		margin: 0.9rem 0 0.6rem 1.1rem;
		padding: 0;
		display: grid;
		gap: 0.4rem;
		font-size: clamp(1rem, 1.6vw, 1.05rem);
	}

	.cta-row {
		margin-top: 1rem;
	}
	.btn {
		display: inline-block;
		padding: 0.7rem 1rem;
		border-radius: 999px;
		text-decoration: none;
		color: #0b0b0b;
		background: #ffffff;
		border: 1px solid rgba(255, 255, 255, 0.9);
		font-weight: 600;
		transition:
			transform 150ms ease,
			opacity 150ms ease;
		will-change: transform;
	}
	.btn:hover,
	.btn:focus-visible {
		transform: translateY(-1px);
		opacity: 0.95;
		outline: none;
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

	/* Mobile: keep dots near edge */
	@media (max-width: 820px) {
		.dots {
			left: 10px;
		}
	}

	/* Accessibility helpers */
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
