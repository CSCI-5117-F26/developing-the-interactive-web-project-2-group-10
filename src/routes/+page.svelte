<script lang="ts">
	import CampusMap from '#lib/components/CampusMap.svelte';
</script>

<svelte:head>
	<title>GopherGo | UMN Gopher Way Map</title>
	<meta
		name="description"
		content="An interactive preview of the UMN Twin Cities Gopher Way network."
	/>
</svelte:head>

<main>
	<CampusMap />
</main>

<style>
	:global(*) {
		box-sizing: border-box;
	}

	:global(body) {
		margin: 0;
		min-width: 320px;
		color: #211a1c;
		background: #f7f4f1;
		font-family:
			Inter,
			ui-sans-serif,
			system-ui,
			-apple-system,
			BlinkMacSystemFont,
			'Segoe UI',
			sans-serif;
	}

	main {
		max-width: 96rem;
		margin: 0 auto;
		padding: 1rem;
	}

	header {
		display: flex;
		align-items: center;
		gap: 0.85rem;
		min-height: 5.5rem;
	}

	.brand-mark {
		display: grid;
		width: 3rem;
		height: 3rem;
		place-items: center;
		border-radius: 0.85rem;
		color: #7a0019;
		background: #ffcc33;
		font-size: 1.25rem;
		font-weight: 850;
	}

	h1 {
		margin: 0;
		color: #7a0019;
		font-size: 1.6rem;
		line-height: 1.1;
	}

	p {
		margin: 0.3rem 0 0;
		color: #6a6062;
		font-size: 0.95rem;
	}

	@media (min-width: 48rem) {
		main {
			padding: 1.25rem 2rem 2rem;
		}
	}
</style>
