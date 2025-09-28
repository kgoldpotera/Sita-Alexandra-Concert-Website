<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	export type MerchItem = { src: string; label: string; href?: string; alt?: string };

	// Replace with your real images in /static/merch/*
	export let items: MerchItem[] = [
		{ src: '/merch/tshirts.jpg', label: 'T-shirts' },
		{ src: '/merch/games.jpg', label: 'Games' },
		{ src: '/merch/comics.jpg', label: 'Comics' },
		{ src: '/merch/sports-kits.jpg', label: 'Sports kits' },
		{ src: '/merch/stickers.jpg', label: 'Stickers' },
		{ src: '/merch/magazines.jpg', label: 'Magazines' },
		{ src: '/merch/flags.jpg', label: 'Flags' },
		{ src: '/merch/headbands.jpg', label: 'Headbands' }
	];

	export let title = 'Explore merchandise';
	export let merchUrl = 'https://homebrandsitaalexandra.com/';

	let container: HTMLElement;
	let ro: ResizeObserver;
	let current = 0;

	// responsive paging (2x2 mobile, 3x2 tablet, 4x2 desktop)
	let cols = 2;
	const rows = 2;
	let pages: MerchItem[][] = [];

	const chunk = <T,>(arr: T[], size: number) => {
		const out: T[][] = [];
		for (let i = 0; i < arr.length; i += size) out.push(arr.slice(i, i + size));
		return out;
	};

	const computeCols = (w: number) => (w >= 1024 ? 4 : w >= 720 ? 3 : 2);

	function recompute(width: number) {
		cols = computeCols(width);
		const size = cols * rows;
		pages = chunk(items, size);
		if (current > pages.length - 1) current = Math.max(0, pages.length - 1);
	}

	const go = (i: number) => (current = (i + pages.length) % pages.length);
	const next = () => go(current + 1);
	const prev = () => go(current - 1);

	// swipe
	let startX = 0;
	const onTouchStart = (e: TouchEvent) => (startX = e.touches[0].clientX);
	const onTouchEnd = (e: TouchEvent) => {
		const dx = e.changedTouches[0].clientX - startX;
		if (Math.abs(dx) > 40) dx < 0 ? next() : prev();
	};

	// fallback for broken images
	const onImgError = (e: Event) => {
		const img = e.currentTarget as HTMLImageElement;
		if (img && img.src.indexOf('placeholder.png') === -1) {
			img.src = '/merch/placeholder.png';
		}
	};

	onMount(() => {
		recompute(container?.clientWidth ?? window.innerWidth);
		ro = new ResizeObserver((entries) => {
			for (const entry of entries) recompute(entry.contentRect.width);
		});
		if (container) ro.observe(container);
		return () => {
			if (ro && container) ro.unobserve(container);
		};
	});

	onDestroy(() => {
		if (ro && container) ro.unobserve(container);
	});
</script>

<section class="explore" bind:this={container}>
	<h2 class="heading">{title}</h2>

	<div class="viewport" on:touchstart={onTouchStart} on:touchend={onTouchEnd}>
		<div class="strip" style={`--i:${current};`}>
			{#each pages as page, pi (pi)}
				<div class="page" aria-hidden={pi === current ? 'false' : 'true'}>
					<div class="grid" style={`--cols:${cols};`}>
						{#each page as it, ii (it.label)}
							<a
								class="card"
								href={it.href ?? merchUrl}
								target="_blank"
								rel="noopener noreferrer"
								aria-label={`Explore ${it.label} on our merchandise site`}
							>
								<div class="imgbox">
									<img
										class="thumb"
										src={it.src}
										alt={it.alt ?? it.label}
										loading="lazy"
										decoding="async"
										on:error={onImgError}
									/>
								</div>
								<span class="label">{it.label}</span>
							</a>
						{/each}
					</div>
				</div>
			{/each}
		</div>

		{#if pages.length > 1}
			<button class="nav prev" aria-label="Previous" on:click={prev}>‹</button>
			<button class="nav next" aria-label="Next" on:click={next}>›</button>
		{/if}
	</div>

	{#if pages.length > 1}
		<div class="pager" role="tablist" aria-label="Select merchandise page">
			{#each pages as _, i (i)}
				<button
					class="bar {i === current ? 'active' : ''}"
					role="tab"
					aria-selected={i === current}
					aria-label={`Go to page ${i + 1}`}
					on:click={() => go(i)}
				/>
			{/each}
		</div>
	{/if}
</section>

<style>
	.explore {
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

	.viewport {
		position: relative;
		overflow: hidden;
		border-radius: var(--radius);
	}
	.strip {
		display: flex;
		width: 100%;
		transform: translateX(calc(-100% * var(--i)));
		transition: transform 0.45s cubic-bezier(0.22, 0.61, 0.36, 1);
	}
	.page {
		flex: 0 0 100%;
		padding: 6px 2px 10px;
	}

	/* responsive grid */
	.grid {
		display: grid;
		grid-template-columns: repeat(var(--cols, 2), 1fr);
		gap: 14px;
	}

	/* cards */
	.card {
		display: grid;
		gap: 10px;
		text-align: center;
		color: inherit;
		text-decoration: none;
	}

	/* square frame + fully visible image */
	.imgbox {
		position: relative;
		width: 100%;
		aspect-ratio: 1 / 1;
		border-radius: 14px;
		background: var(--white-background);
		border: 1px solid var(--white-border);
		overflow: hidden;

		/* center content so letterboxing is even */
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.thumb {
		/* Do NOT force both dimensions to 100% — that’s what “zooms” things */
		width: auto;
		height: auto;
		max-width: 100%;
		max-height: 100%;
		object-fit: contain;
		object-position: center;
		display: block;
		padding: 6px; /* small breathing room; adjust if you want edge-to-edge */
	}

	.label {
		font-family: 'SF Pro Display', 'CircularStd', system-ui, sans-serif;
		font-weight: 600;
		font-size: clamp(14px, 3.2vw, 16px);
	}

	/* pager bars */
	.pager {
		margin-top: 16px;
		display: flex;
		justify-content: center;
		gap: 10px;
		align-items: center;
	}
	.bar {
		width: 44px;
		height: 8px;
		border-radius: 999px;
		background: rgba(0, 0, 0, 0.22);
		border: 1px solid rgba(0, 0, 0, 0.28);
		box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.25);
		cursor: pointer;
	}
	.bar.active {
		background: var(--accent);
		border-color: var(--accent);
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
	}

	/* arrows */
	.nav {
		position: absolute;
		top: 50%;
		translate: 0 -50%;
		width: 36px;
		height: 36px;
		border-radius: 999px;
		border: 1px solid rgba(0, 0, 0, 0.25);
		background: rgba(255, 255, 255, 0.9);
		color: #111;
		display: grid;
		place-items: center;
		cursor: pointer;
		z-index: 2;
	}
	.prev {
		left: 8px;
	}
	.next {
		right: 8px;
	}

	/* tablet/desktop tweaks */
	@media (min-width: 720px) {
		.grid {
			gap: 16px;
		}
		.imgbox {
			border-radius: 16px;
		}
		.label {
			font-size: clamp(14px, 2vw, 17px);
		}
		.bar {
			width: 52px;
		}
	}
	@media (min-width: 1024px) {
		.heading {
			font-size: clamp(26px, 3vw, 34px);
		}
		.label {
			font-size: 17px;
		}
		.bar {
			width: 60px;
		}
	}

	/* reduced motion */
	@media (prefers-reduced-motion: reduce) {
		.strip {
			transition: none;
		}
	}
</style>
