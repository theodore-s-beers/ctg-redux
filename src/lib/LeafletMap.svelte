<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import type { Map } from 'leaflet';

	import { entries, keywordsMap } from '$lib/stores.svelte';

	import {
		fetchList,
		filterPlaces,
		fetchEntries,
		getKeywords
	} from '$lib/utils.svelte';
	import type { JsonStuff } from '$lib/utils.svelte';

	let mapElement: HTMLElement;
	let map: Map;

	let entriesValue: [string, JsonStuff][];
	entries.subscribe((value) => {
		entriesValue = value;
	});

	onMount(async () => {
		const leaflet = await import('leaflet');

		leaflet.Icon.Default.imagePath = 'leaflet/';

		map = leaflet.map(mapElement).setView([52.4543, 13.292], 6);

		leaflet
			.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			})
			.addTo(map);

		const [count, listData] = await fetchList();

		if (entriesValue.length !== count) {
			const freshEntries = await fetchEntries(listData);
			const freshKeywords = getKeywords(freshEntries);

			entries.set(freshEntries);
			keywordsMap.set(freshKeywords);
		}

		for (const [, data] of entriesValue) {
			const places = filterPlaces(data.project.places);

			for (const place of places) {
				leaflet
					.marker([place.coordinates.lat, place.coordinates.lng])
					.addTo(map)
					.bindPopup(data.project.title);
			}
		}
	});

	onDestroy(async () => {
		if (map) {
			map.remove();
		}
	});
</script>

<div class="mx-auto max-w-6xl px-4">
	<div bind:this={mapElement} class="h-[32rem] rounded-md" />
</div>

<style>
	@import 'leaflet/dist/leaflet.css';
</style>
