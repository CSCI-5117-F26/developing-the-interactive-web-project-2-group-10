<script lang="ts">
	import 'leaflet/dist/leaflet.css';
	import { onMount } from 'svelte';
	import type { GeoJsonObject } from 'geojson';
	import type { Map } from 'leaflet';

	const GOPHER_WAY_URL =
		'https://services1.arcgis.com/NEfPUcqTuMvqwoCp/arcgis/rest/services/GW_ROUTE/FeatureServer/4/query?where=1%3D1&outFields=OBJECTID%2CSITE_BUILDING_FLOOR%2CBLDG_NAME_LABEL&returnGeometry=true&outSR=4326&resultRecordCount=2000&f=geojson';

	let mapElement: HTMLDivElement;
	let status = $state<'loading' | 'ready' | 'error'>('loading');

	onMount(() => {
		let map: Map | undefined;
		let disposed = false;

		async function initializeMap() {
			try {
				const L = await import('leaflet');

				if (disposed) return;

				map = L.map(mapElement, {
					minZoom: 13,
					maxZoom: 19,
					zoomControl: true
				}).setView([44.974, -93.234], 16);

				L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
					attribution: '&copy; OpenStreetMap contributors',
					maxZoom: 19
				}).addTo(map);

				const response = await fetch(GOPHER_WAY_URL);
				if (!response.ok) {
					throw new Error(`Gopher Way request failed with status ${response.status}`);
				}

				const gopherWay = (await response.json()) as GeoJsonObject;

				if (disposed) return;

				L.geoJSON(gopherWay, {
					style: {
						color: '#7a0019',
						opacity: 0.9,
						weight: 5
					}
				}).addTo(map);

				status = 'ready';
			} catch (error) {
				console.error('Unable to load the campus map.', error);
				status = 'error';
			}
		}

		void initializeMap();

		return () => {
			disposed = true;
			map?.remove();
		};
	});
</script>

<section class="map-card" aria-label="UMN Twin Cities Gopher Way map">
	<div class="map" bind:this={mapElement}></div>

	<div class="legend" aria-label="Map legend">
		<span class="legend-line"></span>
		<span>Gopher Way</span>
	</div>

	{#if status === 'loading'}
		<p class="status" aria-live="polite">Loading Gopher Way data...</p>
	{:else if status === 'error'}
		<p class="status status-error" role="alert">
			The base map is available, but the Gopher Way data could not be loaded.
		</p>
	{/if}
</section>

<style>
	.map-card {
		position: relative;
		min-height: 32rem;
		height: calc(100dvh - 8.5rem);
		overflow: hidden;
		border: 1px solid #d8d2d3;
		border-radius: 1.25rem;
		background: #e9e6e2;
		box-shadow: 0 1rem 2.5rem rgb(70 39 45 / 12%);
	}

	.map {
		width: 100%;
		height: 100%;
	}

	.legend,
	.status {
		position: absolute;
		z-index: 500;
		left: 1rem;
		margin: 0;
		border: 1px solid rgb(122 0 25 / 15%);
		border-radius: 0.7rem;
		background: rgb(255 255 255 / 94%);
		box-shadow: 0 0.35rem 1rem rgb(56 30 36 / 12%);
	}

	.legend {
		bottom: 1rem;
		display: flex;
		align-items: center;
		gap: 0.65rem;
		padding: 0.65rem 0.8rem;
		font-size: 0.9rem;
		font-weight: 700;
	}

	.legend-line {
		width: 2rem;
		border-top: 0.3rem solid #7a0019;
		border-radius: 999px;
	}

	.status {
		top: 1rem;
		right: 1rem;
		padding: 0.75rem 1rem;
		color: #4b4244;
	}

	.status-error {
		color: #7a0019;
		border-color: rgb(122 0 25 / 30%);
	}

	@media (max-width: 40rem) {
		.map-card {
			height: calc(100dvh - 7.5rem);
			min-height: 28rem;
			border-radius: 1rem;
		}

		.status {
			max-width: calc(100% - 2rem);
		}
	}
</style>
