<script lang="ts">
	import HeroTopbar from '$lib/components/HeroTopbar.svelte';
	import { resolve } from '$app/paths';

	type Block = { title: string; note: string; email: string };

	const blocks: Block[] = [
		{
			title: 'Artist Enquiries',
			note: 'For enquiries kindly contact',
			email: 'contact@rocknrollsuperstars.management'
		},
		{
			title: 'Tour Enquiries & Sponsorship Opportunities',
			note: 'For enquiries kindly contact',
			email: 'info@thechampionshipstadiumconcerts.com'
		}
	];
</script>

<HeroTopbar position="sticky" />

<main class="apply">
	<section class="wrap" aria-labelledby="contact-title">
		<h1 id="contact-title" class="page-title">Get in touch</h1>

		<div class="cards">
			{#each blocks as b (b.email)}
				<article class="card">
					<h2 class="card-title">{b.title}</h2>
					<p class="muted">{b.note}</p>

					<a class="email" href={`mailto:${b.email}`}>
						<svg class="ico" viewBox="0 0 24 24" aria-hidden="true">
							<path
								d="M3.5 6.75A2.25 2.25 0 0 1 5.75 4.5h12.5A2.25 2.25 0 0 1 20.5 6.75v10.5A2.25 2.25 0 0 1 18.25 19.5H5.75A2.25 2.25 0 0 1 3.5 17.25V6.75Zm1.9.02 6.12 4.2a1.25 1.25 0 0 0 1.44 0l6.14-4.2"
								fill="none"
								stroke="currentColor"
								stroke-width="1.6"
								stroke-linecap="round"
								stroke-linejoin="round"
							/>
						</svg>
						<span>{b.email}</span>
					</a>
				</article>
			{/each}
		</div>
	</section>
</main>

<style>
	/* Palette fallbacks */
	:root {
		--sunset: var(--sunset, #f2c879);
		--madder: var(--madder, #a60530);
		--bistre: var(--bistre, #2b1c14);
	}

	/* Page */
	.apply {
		width: 100%;
		min-height: 100vh;
		box-sizing: border-box;
		padding: clamp(20px, 4vw, 36px) clamp(14px, 4vw, 24px);
		overflow-x: hidden; /* ← prevents visual cut on the right */
		background: #ffffff; /* legible by default */
		color: #1b1b1b;
	}
	:root[data-theme='dark'] .apply {
		background: #0b0b0b;
		color: #f5f5f5;
	}

	.wrap {
		width: min(860px, 100%);
		margin-inline: auto;
	}

	/* Heading */
	.page-title {
		margin: 0 0 12px;
		font-weight: 900;
		letter-spacing: -0.02em;
		line-height: 1.05;
		font-size: clamp(22px, 4.8vw, 34px);
		padding-bottom: 6px;
		position: relative;
		display: inline-block;
	}
	.page-title::after {
		content: '';
		position: absolute;
		left: 0;
		right: 0;
		bottom: 0;
		height: 2px;
		border-radius: 2px;
		background: color-mix(in srgb, var(--sunset) 85%, #fff 15%);
	}

	/* Cards */
	.cards {
		display: grid;
		gap: 16px;
	}

	.card {
		background: #fff;
		color: #1b1b1b;
		border: 1px solid #e7e7e7;
		border-radius: 16px;
		box-shadow: 0 8px 24px rgba(0, 0, 0, 0.06);
		padding: clamp(14px, 3vw, 20px);
	}
	:root[data-theme='dark'] .card {
		background: color-mix(in srgb, #000 35%, transparent);
		border-color: color-mix(in srgb, #fff 12%, transparent);
		box-shadow: none;
		color: #f5f5f5;
	}

	.card-title {
		margin: 0 0 6px;
		font-weight: 800;
		letter-spacing: -0.01em;
		font-size: clamp(16px, 2.6vw, 20px);
	}

	.muted {
		margin: 0 0 10px;
		color: #4e5d70;
	}
	:root[data-theme='dark'] .muted {
		color: #d1d1d1;
	}

	/* Email pill */
	.email {
		display: inline-flex;
		align-items: center;
		gap: 10px;
		max-width: 100%;
		padding: 10px 14px;
		border-radius: 12px;
		text-decoration: none;
		color: #1b1b1b;
		background: #fff;
		border: 1px solid #e7e7e7;
		transition:
			box-shadow 180ms ease,
			transform 120ms ease;
	}
	.email .ico {
		width: 18px;
		height: 18px;
		color: var(--madder);
		flex: 0 0 auto;
	}
	.email span {
		overflow-wrap: anywhere;
	} /* ← long address wraps, never overflows */
	.email:hover,
	.email:focus-visible {
		box-shadow: 0 0 0 4px color-mix(in srgb, var(--sunset) 24%, transparent);
		transform: translateY(-1px);
		outline: none;
	}

	:root[data-theme='dark'] .email {
		background: color-mix(in srgb, #fff 8%, transparent);
		border-color: color-mix(in srgb, #fff 18%, transparent);
		color: #fff;
	}

	/* Back link */
	.back {
		display: inline-block;
		margin-top: 10px;
		color: inherit;
		text-decoration: none;
		opacity: 0.9;
	}
	.back:hover {
		text-decoration: underline;
	}
</style>
