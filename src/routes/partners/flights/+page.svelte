<script lang="ts">
	import HeroTopbar from '$lib/components/HeroTopbar.svelte';
	import { resolve } from '$app/paths';

	const titleText = 'Book your flight to see SitaAlexandra live.';
	const leadText =
		'She’s bringing world-class performances to cities across the tour. Fly in, catch the show, and make a memory. Look out for exclusive airline coupons when you book through our partner links.';
	const heroAltText = 'Airplane flying above the clouds';
	const goToText = 'Go to';
	const couponText = 'Coupon';
	const couponCopiedText = 'Coupon Copied!';
	const takingYouText = 'Taking you to the airline in a moment…';
	const useCodePrefixText = 'Use code';
	const ariaCouponCodeText = 'Coupon code';
	const viewCitiesText = 'View cities';

	const hero = {
		image: '/airplane-hero.jpg',
		mag: { x: '66%', y: '38%', size: 160, zoom: 1.6 }
	};

	type Airline = {
		name: string;
		url: string; // note: these are external (https://…)
		coupon: string;
		logo: string;
		top: string;
		left: string;
	};

	const airlines: Airline[] = [
		{
			name: 'Qatar Airways',
			url: 'https://www.qatarairways.com/',
			coupon: 'SITA10',
			logo: '/logos/qatar-airways.svg',
			top: '20%',
			left: '78%'
		},
		{
			name: 'Emirates',
			url: 'https://www.emirates.com/',
			coupon: 'SITA10',
			logo: '/logos/emirates.svg',
			top: '33%',
			left: '78%'
		},
		{
			name: 'Turkish Airlines',
			url: 'https://www.turkishairlines.com/',
			coupon: 'SITA10',
			logo: '/logos/turkishairlines.svg',
			top: '46%',
			left: '78%'
		},
		{
			name: 'British Airways',
			url: 'https://www.britishairways.com/',
			coupon: 'SITA10',
			logo: '/logos/britishairways.svg',
			top: '59%',
			left: '78%'
		}
	];

	// Confirmation overlay state
	let confirmVisible = false;
	let confirmName = '';
	let confirmCode = '';
	let confirmLogo = '';
	let redirectTo = '';
	let redirectTimer: ReturnType<typeof setTimeout> | null = null;

	function showConfirm(a: Airline, copied: boolean) {
		confirmName = a.name;
		confirmCode = copied ? a.coupon : `${useCodePrefixText}: ${a.coupon}`;
		confirmLogo = a.logo;
		redirectTo = a.url;
		confirmVisible = true;

		if (redirectTimer) clearTimeout(redirectTimer);
		// runs in the browser after the click
		redirectTimer = setTimeout(() => {
			window.location.href = redirectTo;
		}, 1500);
	}

	async function handleAirlineClick(a: Airline) {
		let copied = false;
		try {
			if (navigator.clipboard?.writeText) {
				await navigator.clipboard.writeText(a.coupon);
				copied = true;
			}
		} catch {
			copied = false;
		}
		showConfirm(a, copied);
	}
</script>

<HeroTopbar position="sticky" />

<main class="flights">
	<section class="hero">
		<div class="copy">
			<h1>{titleText}</h1>
			<p>{leadText}</p>
			<!-- ✅ use resolve() only with a literal route -->
			<a class="cta" href={resolve('/cities')}>
				{viewCitiesText} <span aria-hidden="true">→</span>
			</a>
		</div>

		<div
			class="media"
			style="background-image: url({hero.image});"
			aria-label={heroAltText}
			role="img"
		>
			<div class="overlay" aria-hidden="true"></div>

			<div
				class="magnifier"
				style="
          left: {hero.mag.x};
          top: {hero.mag.y};
          width: {hero.mag.size}px;
          height: {hero.mag.size}px;
          background-image: url({hero.image});
          --zoom: {hero.mag.zoom};
          --mag-x: {hero.mag.x};
          --mag-y: {hero.mag.y};
        "
				aria-hidden="true"
			></div>

			<div id="airlines" class="chips">
				{#each airlines as a, i (a.name || i)}
					<div class="chip-wrap" style="top:{a.top}; left:{a.left};">
						<!--
              ❗ Dynamic/external URL: DO NOT call resolve() (causes TS error).
              If the linter still complains, we explicitly ignore it here.
            -->
						<!-- svelte-ignore svelte/no-navigation-without-resolve -->
						<a
							class="chip"
							href={a.url}
							target="_blank"
							rel="nofollow noopener noreferrer"
							on:click|preventDefault={() => handleAirlineClick(a)}
							title={`${goToText} ${a.name}. ${couponText}: ${a.coupon}`}
						>
							<img
								class="logo"
								src={a.logo}
								alt=""
								on:error={(e) => {
									(e.currentTarget as HTMLImageElement).style.display = 'none';
								}}
							/>
							<span>{a.name}</span>
						</a>
					</div>
				{/each}
			</div>
		</div>
	</section>
</main>

{#if confirmVisible}
	<div class="confirm" role="dialog" aria-modal="true" aria-live="polite">
		<div class="glow" aria-hidden="true"></div>
		<div class="card">
			<svg class="check" width="60" height="60" viewBox="0 0 60 60" aria-hidden="true">
				<circle class="ring" cx="30" cy="30" r="26" fill="none" stroke-width="4"></circle>
				<path
					class="tick"
					d="M18 32 L27 40 L42 22"
					fill="none"
					stroke-width="5"
					stroke-linecap="round"
					stroke-linejoin="round"
				></path>
			</svg>

			<h2>{couponCopiedText}</h2>
			<p class="brand">
				{#if confirmLogo}<img class="brand-logo" src={confirmLogo} alt="" />{/if}
				<span>{confirmName}</span>
			</p>

			<div class="code-pill" aria-label={`${ariaCouponCodeText} ${confirmCode}`}>
				<code>{confirmCode}</code>
				<div class="shine" aria-hidden="true"></div>
			</div>

			<p class="note">{takingYouText}</p>
		</div>
	</div>
{/if}

<style>
	/* (styles unchanged from your last version) */
	.flights {
		background: #f4f7fb;
		min-height: 100vh;
		display: flex;
		flex-direction: column;
	}
	.hero {
		display: grid;
		grid-template-columns: 1fr 1.1fr;
		gap: 3rem;
		align-items: center;
		padding: 3.5rem min(8vw, 7rem);
	}
	.copy h1 {
		margin: 0 0 0.75rem;
		font-size: clamp(1.6rem, 1.1rem + 1.5vw, 2.3rem);
		line-height: 1.2;
		color: #1e2f44;
	}
	.copy p {
		margin: 0 0 1.25rem;
		color: #4e5d70;
		font-size: 1rem;
		line-height: 1.6;
		max-width: 48ch;
	}
	.cta {
		display: inline-flex;
		align-items: center;
		gap: 0.5rem;
		background: #ffd84d;
		color: #202020;
		text-decoration: none;
		font-weight: 600;
		padding: 0.8rem 1.2rem;
		border-radius: 999px;
		box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08);
		transition:
			transform 0.15s ease,
			box-shadow 0.2s ease,
			background 0.2s ease;
	}
	.cta:hover {
		transform: translateY(-1px);
		box-shadow: 0 10px 22px rgba(0, 0, 0, 0.12);
	}
	.media {
		position: relative;
		min-height: 440px;
		border-radius: 22px;
		overflow: hidden;
		background-size: cover;
		background-position: center;
		box-shadow: 0 18px 40px rgba(26, 46, 77, 0.18);
	}
	.overlay {
		position: absolute;
		inset: 0;
		z-index: 1;
		background: linear-gradient(
			90deg,
			rgba(26, 46, 77, 0.35) 0%,
			rgba(26, 46, 77, 0.1) 35%,
			rgba(26, 46, 77, 0) 55%
		);
		pointer-events: none;
	}
	.magnifier {
		position: absolute;
		z-index: 2;
		transform: translate(-50%, -50%);
		border-radius: 50%;
		border: 4px solid rgba(255, 255, 255, 0.85);
		box-shadow:
			0 18px 36px rgba(0, 0, 0, 0.18),
			inset 0 0 0 1px rgba(0, 0, 0, 0.04);
		background-repeat: no-repeat;
		background-size: calc(100% * var(--zoom)) auto;
		background-position: var(--mag-x) var(--mag-y);
	}
	.chips {
		position: absolute;
		inset: 0;
		pointer-events: none;
		z-index: 3;
	}
	.chip-wrap {
		position: absolute;
		transform: translate(-50%, -50%);
		pointer-events: auto;
	}
	.chip {
		display: inline-flex;
		align-items: center;
		gap: 0.5rem;
		padding: 0.4rem 0.8rem;
		background: #fff;
		color: #1e2f44;
		border-radius: 999px;
		text-decoration: none;
		font-size: 0.95rem;
		box-shadow: 0 10px 24px rgba(30, 47, 68, 0.12);
		transition:
			transform 0.15s ease,
			box-shadow 0.2s ease;
		white-space: nowrap;
	}
	.chip:hover {
		transform: translateY(-1px);
		box-shadow: 0 14px 28px rgba(30, 47, 68, 0.16);
	}
	.logo {
		height: 14px;
		width: auto;
		max-width: 80px;
		display: block;
		object-fit: contain;
		filter: drop-shadow(0 0 1px rgba(0, 0, 0, 0.35));
		flex-shrink: 0;
	}
	.confirm {
		position: fixed;
		inset: 0;
		z-index: 40;
		display: grid;
		place-items: center;
		background: rgba(10, 18, 28, 0.28);
		backdrop-filter: blur(6px);
		animation: fadeIn 180ms ease-out;
	}
	.glow {
		position: absolute;
		inset: -20%;
		background:
			radial-gradient(60% 40% at 50% 40%, rgba(255, 216, 77, 0.25), transparent 60%),
			radial-gradient(50% 50% at 70% 60%, rgba(35, 56, 75, 0.25), transparent 60%);
		filter: blur(40px);
		pointer-events: none;
	}
	.card {
		position: relative;
		background: #fff;
		border-radius: 18px;
		box-shadow: 0 40px 70px rgba(0, 0, 0, 0.2);
		padding: 1.2rem 1.4rem 1.1rem;
		min-width: 280px;
		max-width: 92vw;
		text-align: center;
		animation: popIn 200ms cubic-bezier(0.2, 0.9, 0.2, 1.2);
	}
	.check {
		display: block;
		margin: 0 auto 0.4rem;
	}
	.ring {
		stroke: #e5eef7;
	}
	.tick {
		stroke: #1f9d55;
		stroke-dasharray: 60;
		stroke-dashoffset: 60;
		animation: draw 500ms ease-out forwards 80ms;
	}
	.card h2 {
		margin: 0.2rem 0 0.2rem;
		font-size: 1.25rem;
		color: #1e2f44;
	}
	.brand {
		margin: 0.1rem 0 0.6rem;
		display: inline-flex;
		align-items: center;
		gap: 0.4rem;
		color: #405066;
	}
	.brand-logo {
		height: 16px;
		width: auto;
		max-width: 90px;
		object-fit: contain;
		filter: drop-shadow(0 0 1px rgba(0, 0, 0, 0.35));
	}
	.code-pill {
		position: relative;
		display: inline-flex;
		align-items: center;
		padding: 0.4rem 0.8rem;
		border-radius: 999px;
		background: #0f172a;
		color: #fff;
		font-weight: 600;
		box-shadow:
			inset 0 0 0 1px rgba(255, 255, 255, 0.08),
			0 8px 24px rgba(0, 0, 0, 0.18);
		overflow: hidden;
	}
	.code-pill code {
		font-family: ui-monospace, SFMono-Regular, Menlo, monospace;
		letter-spacing: 0.2px;
	}
	.shine {
		position: absolute;
		inset: 0;
		background: linear-gradient(
			120deg,
			transparent 0%,
			rgba(255, 255, 255, 0.25) 35%,
			transparent 70%
		);
		background-size: 200% 100%;
		animation: shimmer 1200ms ease-in-out 1 both 160ms;
		pointer-events: none;
	}
	.note {
		margin: 0.6rem 0 0;
		font-size: 0.9rem;
		color: #4e5d70;
	}
	@keyframes fadeIn {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}
	@keyframes popIn {
		from {
			transform: scale(0.92);
			opacity: 0;
		}
		to {
			transform: scale(1);
			opacity: 1;
		}
	}
	@keyframes draw {
		to {
			stroke-dashoffset: 0;
		}
	}
	@keyframes shimmer {
		from {
			background-position: 200% 0;
		}
		to {
			background-position: -100% 0;
		}
	}
	@media (prefers-reduced-motion: reduce) {
		.confirm,
		.card,
		.tick,
		.shine {
			animation: none !important;
		}
	}
	@media (max-width: 980px) {
		.hero {
			grid-template-columns: 1fr;
			gap: 1.75rem;
		}
		.copy {
			order: 2;
		}
		.media {
			order: 1;
			min-height: 360px;
		}
	}
	@media (max-width: 640px) {
		.copy h1 {
			font-size: 1.35rem;
		}
		.copy p {
			font-size: 0.95rem;
		}
		.magnifier {
			display: none;
		}
		.chip {
			font-size: 0.9rem;
			padding: 0.35rem 0.7rem;
		}
		.logo {
			height: 12px;
			max-width: 70px;
		}
	}
</style>
