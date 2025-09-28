<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { resolve, base } from '$app/paths';
	import { page } from '$app/stores';
	import { browser } from '$app/environment';

	export let height: number | string = 42;
	export let position: 'absolute' | 'sticky' = 'sticky';
	export let gradient = true;
	export let className = '';

	// center slot is opt-in
	export let showCenter = false;

	// Optional manual override; else auto-hide on home
	export let showHome: boolean | undefined = undefined;

	export let buyLabel = 'Buy Tickets';
	export let vendors: Array<{ label: string; href: string; disabled?: boolean }> = [
		{ label: 'Eventbrite', href: 'https://www.eventbrite.com/' },
		{ label: 'Ticket Site 2', href: '#', disabled: true },
		{ label: 'Ticket Site 3', href: '#', disabled: true }
	];

	$: h = typeof height === 'number' ? `${height}px` : height;
	const isExternal = (u: string) => /^(https?:\/\/|mailto:|tel:)/i.test(u);

	// Home detection (strip trailing slash)
	$: isHomeRoute = $page.url.pathname.replace(/\/+$/, '') === (base || '').replace(/\/+$/, '');
	$: shouldShowHome = showHome ?? !isHomeRoute;

	let showVendors = false;
	const toggleVendors = () => (showVendors = !showVendors);

	let root: HTMLElement;
	const onDocClick = (e: MouseEvent) => {
		if (!root?.contains(e.target as Node)) showVendors = false;
	};
	const onKey = (e: KeyboardEvent) => {
		if (e.key === 'Escape') showVendors = false;
	};

	onMount(() => {
		if (!browser) return;
		document.addEventListener('click', onDocClick);
		document.addEventListener('keydown', onKey);
	});
	onDestroy(() => {
		if (!browser) return;
		document.removeEventListener('click', onDocClick);
		document.removeEventListener('keydown', onKey);
	});
</script>

<nav
	bind:this={root}
	class="topbar {showCenter ? 'has-center' : 'no-center'} {position === 'sticky'
		? 'is-sticky'
		: 'is-absolute'} {gradient ? '' : 'no-gradient'} {className}"
	aria-label="Site controls"
	style={`--topbar-h:${h}`}
>
	<div class="zone left">
		{#if shouldShowHome}
			<a class="home" href={resolve('/')} aria-label="Go to home">
				<svg viewBox="0 0 24 24" aria-hidden="true">
					<path
						d="M3 11.5 12 4l9 7.5v7a1.5 1.5 0 0 1-1.5 1.5H14a1 1 0 0 1-1-1v-5h-2v5a1 1 0 0 1-1 1H4.5A1.5 1.5 0 0 1 3 18.5v-7z"
					/>
				</svg>
			</a>
		{/if}
		<slot name="left" />
	</div>

	{#if showCenter}
		<div class="zone center"><slot name="center" /></div>
	{/if}

	<div class="zone right">
		<!-- Button instead of <a> to avoid resolve()/lint errors -->
		<button
			class="cta buy"
			type="button"
			aria-haspopup="menu"
			aria-expanded={showVendors}
			aria-controls="ticket-providers"
			on:click={toggleVendors}
		>
			{buyLabel}
		</button>
	</div>

	<div
		id="ticket-providers"
		class="vendors-popover"
		data-open={showVendors}
		aria-hidden={!showVendors}
	>
		{#each vendors as v, i (v.label ?? i)}
			{#if v.disabled}
				<span class="cta vendor disabled" role="link" aria-disabled="true" tabindex="-1">
					{v.label}
				</span>
			{:else if isExternal(v.href)}
				<a class="cta vendor" href={v.href} target="_blank" rel="noopener noreferrer">
					{v.label}
				</a>
			{:else}
				<!-- optional internal fallback if you ever pass an internal vendor link -->
				<a class="cta vendor" href={resolve('/sponsors')}>{v.label}</a>
			{/if}
		{/each}
	</div>
</nav>

<style>
	.topbar {
		position: relative;
		width: 100%;
		display: grid;
		align-items: center;
		height: var(--topbar-h, 42px);
		padding: 0 10px;
		border-bottom: 1px solid rgba(255, 255, 255, 0.18);
		z-index: 20;
		background: var(--topbar-bg, linear-gradient(to bottom, rgba(0, 0, 0, 0.65), rgba(0, 0, 0, 0)));
		overflow-x: clip;
	}
	.topbar.no-center {
		grid-template-columns: 1fr auto;
	}
	.topbar.has-center {
		grid-template-columns: 1fr auto 1fr;
	}
	.topbar.no-gradient {
		background: none;
	}
	.is-absolute {
		position: absolute;
		inset: 0 0 auto 0;
	}
	.is-sticky {
		position: sticky;
		top: 0;
	}

	.zone {
		min-width: 0;
	}
	.zone.left {
		display: flex;
		align-items: center;
		gap: 10px;
	}
	.zone.right {
		justify-self: end;
		display: flex;
		align-items: center;
		gap: 10px;
	}

	.home {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		width: 36px;
		height: 36px;
		border-radius: 999px;
		border: 1px solid rgba(255, 255, 255, 0.35);
		background: rgba(0, 0, 0, 0.25);
		color: #fff;
		text-decoration: none;
		transition:
			transform 0.15s ease,
			opacity 0.15s ease;
	}
	.home:hover {
		transform: translateY(-1px);
		opacity: 0.95;
	}
	.home svg {
		width: 18px;
		height: 18px;
		fill: currentColor;
	}

	.topbar .cta {
		display: inline-block;
		text-decoration: none;
		border-radius: 999px;
		font-weight: 700;
		padding: 10px 14px;
		border: 1px solid var(--border, rgba(0, 0, 0, 0.08));
		color: #1b1b1b;
		background: linear-gradient(180deg, #f4c872 0%, #f7dba6 100%);
		box-shadow: 0 1px 0 rgba(0, 0, 0, 0.04);
		transition:
			transform 0.15s ease,
			opacity 0.15s ease,
			box-shadow 0.15s ease;
		white-space: nowrap;
	}
	.topbar .cta:hover {
		transform: translateY(-1px);
		opacity: 0.95;
	}
	.topbar .cta.buy {
		padding-inline: 14px;
	}

	.vendors-popover {
		position: absolute;
		top: var(--topbar-h, 42px);
		right: 10px;
		display: grid;
		grid-template-columns: 1fr;
		gap: 8px;
		width: min(92vw, 360px);
		max-width: calc(100vw - 20px);
		max-height: 0;
		opacity: 0;
		overflow: hidden;
		pointer-events: none;
		transition:
			max-height 260ms ease,
			opacity 220ms ease,
			margin-top 220ms ease;
		margin-top: 0;
		padding: 0 2px;
		z-index: 30;
	}
	.vendors-popover[data-open='true'] {
		max-height: 360px;
		opacity: 1;
		pointer-events: auto;
		margin-top: 8px;
	}

	.vendor {
		display: block;
		text-align: center;
		padding: 12px 16px;
		background: linear-gradient(180deg, #f4c872 0%, #f7dba6 100%);
		color: #1b1b1b;
	}
	.vendor.disabled {
		background: linear-gradient(180deg, #f6e5bf 0%, #fbf1d9 100%);
		color: rgba(27, 27, 27, 0.55);
		border-color: rgba(0, 0, 0, 0.06);
		pointer-events: none;
	}

	:root[data-theme='dark'] .topbar .cta,
	:root[data-theme='dark'] .vendor {
		color: #111;
		border-color: rgba(255, 255, 255, 0.08);
	}
</style>
