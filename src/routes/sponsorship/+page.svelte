<script lang="ts">
	import HeroTopbar from '$lib/components/HeroTopbar.svelte';
	import SiteFooter from '$lib/components/SiteFooter.svelte';

	let formData = {
		name: '',
		email: '',
		company: '',
		phone: '',
		message: ''
	};

	let isSubmitting = false;
	let submitStatus: 'idle' | 'success' | 'error' = 'idle';

	const images = [
		'/sponsor/WhatsApp Image 2025-11-21 at 3.59.16 PM.jpeg',
		'/sponsor/WhatsApp Image 2025-11-21 at 3.59.39 PM.jpeg',
		'/sponsor/WhatsApp Image 2025-11-21 at 4.01.01 PM.jpeg',
		'/sponsor/WhatsApp Image 2025-11-21 at 4.01.39 PM.jpeg'
	];

	async function handleSubmit(e: Event) {
		e.preventDefault();
		isSubmitting = true;
		submitStatus = 'idle';

		const message = `*New Sponsorship Inquiry*\n\n*Name:* ${formData.name}\n*Email:* ${formData.email}\n*Company:* ${formData.company}\n*Phone:* ${formData.phone}\n\n*Message:*\n${formData.message}`;

		const encodedMessage = encodeURIComponent(message);
		const whatsappUrl = `https://wa.me/254706487376?text=${encodedMessage}`;

		window.open(whatsappUrl, '_blank');

		formData = {
			name: '',
			email: '',
			company: '',
			phone: '',
			message: ''
		};
		isSubmitting = false;
		submitStatus = 'success';

		setTimeout(() => {
			submitStatus = 'idle';
		}, 5000);
	}
</script>

<HeroTopbar position="sticky" />

<main class="sponsorship-page">
	<section class="hero-section">
		<div class="container">
			<h1 class="page-title">Partnership Opportunities</h1>
			<p class="page-subtitle">
				Join us in creating unforgettable experiences at the Championship Stadium World Tour
			</p>
		</div>
	</section>

	<section class="images-section">
		<div class="container">
			<div class="images-grid">
				{#each images as image, i (image)}
					<div class="image-card">
						<img src={image} alt={`Partnership opportunity ${i + 1}`} loading="lazy" />
					</div>
				{/each}
			</div>
		</div>
	</section>

	<section class="contact-section">
		<div class="container">
			<div class="form-wrapper">
				<h2 class="section-title">Get in Touch</h2>
				<p class="section-subtitle">
					Interested in becoming a sponsor? Fill out the form below and we'll get back to you.
				</p>

				<form on:submit={handleSubmit} class="contact-form">
					<div class="form-group">
						<label for="name">Full Name *</label>
						<input
							type="text"
							id="name"
							bind:value={formData.name}
							required
							placeholder="Your name"
						/>
					</div>

					<div class="form-group">
						<label for="email">Email Address *</label>
						<input
							type="email"
							id="email"
							bind:value={formData.email}
							required
							placeholder="your@email.com"
						/>
					</div>

					<div class="form-group">
						<label for="company">Company Name *</label>
						<input
							type="text"
							id="company"
							bind:value={formData.company}
							required
							placeholder="Your company"
						/>
					</div>

					<div class="form-group">
						<label for="phone">Phone Number</label>
						<input type="tel" id="phone" bind:value={formData.phone} placeholder="+254..." />
					</div>

					<div class="form-group">
						<label for="message">Message *</label>
						<textarea
							id="message"
							bind:value={formData.message}
							required
							rows="5"
							placeholder="Tell us about your sponsorship interests..."
						></textarea>
					</div>

					<button type="submit" class="submit-btn" disabled={isSubmitting}>
						{isSubmitting ? 'Submitting...' : 'Send Message'}
					</button>

					{#if submitStatus === 'success'}
						<div class="status-message success">
							Message sent successfully! We'll get back to you soon.
						</div>
					{:else if submitStatus === 'error'}
						<div class="status-message error">
							Something went wrong. Please try again or contact us directly.
						</div>
					{/if}
				</form>
			</div>
		</div>
	</section>
</main>

<SiteFooter />

<style>
	:root {
		--sunset: #f2c879;
		--madder: #a60530;
		--bistre: #2b1c14;
	}

	.sponsorship-page {
		width: 100%;
		min-height: 100vh;
		background: #ffffff;
		color: #1b1b1b;
	}

	.container {
		max-width: 1200px;
		margin: 0 auto;
		padding: 0 clamp(16px, 4vw, 24px);
	}

	.hero-section {
		padding: clamp(40px, 8vw, 80px) 0 clamp(30px, 6vw, 60px);
		text-align: center;
		background: linear-gradient(180deg, rgba(242, 200, 121, 0.08) 0%, transparent 100%);
	}

	.page-title {
		margin: 0 0 16px;
		font-size: clamp(32px, 6vw, 56px);
		font-weight: 900;
		letter-spacing: -0.02em;
		line-height: 1.1;
		color: var(--bistre);
	}

	.page-subtitle {
		margin: 0;
		font-size: clamp(16px, 2.5vw, 20px);
		line-height: 1.6;
		color: #4e5d70;
		max-width: 65ch;
		margin-inline: auto;
	}

	.images-section {
		padding: clamp(40px, 8vw, 80px) 0;
	}

	.images-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: clamp(16px, 3vw, 24px);
	}

	.image-card {
		position: relative;
		width: 100%;
		aspect-ratio: 4 / 3;
		border-radius: 16px;
		overflow: hidden;
		background: #f5f5f5;
		box-shadow: 0 8px 24px rgba(0, 0, 0, 0.08);
		transition:
			transform 0.3s ease,
			box-shadow 0.3s ease;
	}

	.image-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 12px 32px rgba(0, 0, 0, 0.12);
	}

	.image-card img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		display: block;
	}

	.contact-section {
		padding: clamp(40px, 8vw, 80px) 0;
		background: linear-gradient(180deg, transparent 0%, rgba(242, 200, 121, 0.06) 100%);
	}

	.form-wrapper {
		max-width: 700px;
		margin: 0 auto;
	}

	.section-title {
		margin: 0 0 12px;
		font-size: clamp(28px, 5vw, 42px);
		font-weight: 800;
		letter-spacing: -0.015em;
		text-align: center;
		color: var(--bistre);
	}

	.section-subtitle {
		margin: 0 0 40px;
		text-align: center;
		color: #4e5d70;
		font-size: clamp(15px, 2vw, 17px);
	}

	.contact-form {
		background: #ffffff;
		border: 1px solid #e5e7eb;
		border-radius: 20px;
		padding: clamp(24px, 5vw, 40px);
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.06);
	}

	.form-group {
		margin-bottom: 24px;
	}

	.form-group label {
		display: block;
		margin-bottom: 8px;
		font-weight: 600;
		color: var(--bistre);
		font-size: 15px;
	}

	.form-group input,
	.form-group textarea {
		width: 100%;
		padding: 12px 16px;
		border: 1.5px solid #e5e7eb;
		border-radius: 12px;
		font-size: 16px;
		font-family: inherit;
		transition:
			border-color 0.2s ease,
			box-shadow 0.2s ease;
		box-sizing: border-box;
	}

	.form-group input:focus,
	.form-group textarea:focus {
		outline: none;
		border-color: var(--sunset);
		box-shadow: 0 0 0 3px rgba(242, 200, 121, 0.2);
	}

	.form-group textarea {
		resize: vertical;
		min-height: 120px;
	}

	.submit-btn {
		width: 100%;
		padding: 14px 24px;
		background: linear-gradient(135deg, var(--madder) 0%, #8a0428 100%);
		color: #ffffff;
		border: none;
		border-radius: 12px;
		font-size: 17px;
		font-weight: 700;
		cursor: pointer;
		transition:
			transform 0.2s ease,
			box-shadow 0.2s ease;
		box-shadow: 0 8px 20px rgba(166, 5, 48, 0.3);
	}

	.submit-btn:hover:not(:disabled) {
		transform: translateY(-2px);
		box-shadow: 0 12px 28px rgba(166, 5, 48, 0.4);
	}

	.submit-btn:disabled {
		opacity: 0.6;
		cursor: not-allowed;
	}

	.status-message {
		margin-top: 16px;
		padding: 12px 16px;
		border-radius: 10px;
		text-align: center;
		font-weight: 600;
	}

	.status-message.success {
		background: #d1fae5;
		color: #065f46;
		border: 1px solid #6ee7b7;
	}

	.status-message.error {
		background: #fee2e2;
		color: #991b1b;
		border: 1px solid #fca5a5;
	}

	@media (max-width: 768px) {
		.images-grid {
			grid-template-columns: repeat(2, 1fr);
			gap: 12px;
		}

		.image-card {
			aspect-ratio: 1 / 1;
		}
	}

	@media (max-width: 480px) {
		.page-title {
			font-size: clamp(24px, 8vw, 32px);
		}

		.form-group input,
		.form-group textarea {
			font-size: 14px;
		}
	}

	:root[data-theme='dark'] .sponsorship-page {
		background: #0b0b0b;
		color: #f5f5f5;
	}

	:root[data-theme='dark'] .contact-form {
		background: color-mix(in srgb, #000 35%, transparent);
		border-color: color-mix(in srgb, #fff 12%, transparent);
	}

	:root[data-theme='dark'] .form-group input,
	:root[data-theme='dark'] .form-group textarea {
		background: color-mix(in srgb, #fff 5%, transparent);
		border-color: color-mix(in srgb, #fff 15%, transparent);
		color: #f5f5f5;
	}

	:root[data-theme='dark'] .form-group label {
		color: #f5f5f5;
	}
</style>
