<!-- src/routes/sponsors/+page.svelte (no i18n; keeps Header/Footer) -->
<script lang="ts">
	// Brand assets
	const brandLogo = '/logos/cscwt-logo.jpg';
	const brandAlt = 'CSCWT by SitaAlexandra';

	// Static copy
	const copy = {
		title: 'Our Partners · Sponsors',
		intro:
			"We're preparing limited sponsor slots for this tour. Until we're ready to announce partners, enjoy our tour mark below.",
		cta: 'Become a sponsor'
	};

	const contactHref = '/contact';
</script>

<main class="wrap" aria-label={copy.title}>
	<section class="container">
		<h1 class="page-title">{copy.title}</h1>
		<p class="lead">{copy.intro}</p>

		<div class="logo-card" role="img" aria-label={brandAlt}>
			<img src={brandLogo} alt={brandAlt} loading="eager" decoding="async" />
		</div>

		<div class="cta-row">
			<a class="cta" href={contactHref}>
				{copy.cta} <span aria-hidden="true">→</span>
			</a>
		</div>
	</section>
</main>

<style>
	:global(html, body) {
		background: #ffffff;
		color: #0f172a;
	}

	.wrap {
		padding-block: clamp(12px, 3vw, 28px);
	}

	.container {
		margin-inline: auto;
		max-inline-size: min(100%, 1100px);
		padding-inline: clamp(12px, 4vw, 24px);
	}

	.page-title {
		margin: 4px 0 10px;
		font-weight: 800;
		letter-spacing: -0.01em;
		font-size: clamp(20px, 2.6vw, 28px);
		color: color-mix(in srgb, var(--c-bistre, #2b1c14) 80%, #000);
	}

	.lead {
		margin: 0 0 clamp(14px, 2.6vw, 22px);
		color: #374151;
		font-size: clamp(14px, 1.7vw, 16px);
		max-width: 65ch;
	}

	/* Elegant logo presentation */
	.logo-card {
		display: grid;
		place-items: center;
		margin-inline: auto;
		border-radius: 26px;
		background: #fff;
		border: 1px solid #e5e7eb;
		box-shadow: 0 12px 30px rgba(16, 24, 40, 0.08);
		padding: clamp(14px, 4vw, 24px);
		inline-size: min(100%, clamp(260px, 72vw, 640px));
	}

	.logo-card img {
		display: block;
		max-inline-size: 100%;
		height: auto; /* preserve aspect ratio */
		object-fit: contain; /* never crop */
	}

	.cta-row {
		display: grid;
		place-items: center;
		margin-top: clamp(12px, 3vw, 18px);
	}

	.cta {
		display: inline-flex;
		align-items: center;
		gap: 0.5rem;
		padding: 0.7rem 1.1rem;
		border-radius: 999px;
		border: 1px solid #e5e7eb;
		background: #fff;
		color: #111827;
		text-decoration: none;
		font-weight: 600;
		transition:
			transform 0.15s,
			box-shadow 0.15s,
			background 0.15s;
	}
	.cta:hover {
		background: #f9fafb;
		box-shadow: 0 8px 16px rgba(16, 24, 40, 0.1);
		transform: translateY(-1px);
	}

	@media (max-width: 640px) {
		.lead {
			max-width: 100%;
		}
		.logo-card {
			border-radius: 20px;
			inline-size: min(100%, clamp(220px, 86vw, 520px));
		}
	}

	/* Dark theme */
	:root[data-theme='dark'] .logo-card {
		background: color-mix(in srgb, #000 40%, transparent);
		border-color: color-mix(in srgb, #fff 12%, transparent);
		box-shadow: 0 16px 36px rgba(0, 0, 0, 0.35);
	}
	:root[data-theme='dark'] .cta {
		background: color-mix(in srgb, #000 35%, transparent);
		border-color: color-mix(in srgb, #fff 12%, transparent);
		color: #e5e7eb;
	}
	:root[data-theme='dark'] .cta:hover {
		background: color-mix(in srgb, #000 20%, transparent);
	}
</style>
