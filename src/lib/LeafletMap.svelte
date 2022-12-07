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

	interface ProjectLink {
		title: string;
		url: string;
	}

	interface PlaceData {
		lat: number;
		lng: number;
		projects: ProjectLink[];
	}

	let mapElement: HTMLElement;
	let map: Map;

	let entriesValue: [string, JsonStuff][];
	entries.subscribe((value) => {
		entriesValue = value;
	});

	const jsonUrlPrefix =
		'https://github.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/blob/master/PROJECTS/';

	const placesMap: Record<string, PlaceData> = {};

	onMount(async () => {
		//
		// Start by loading the Leaflet map
		//

		const leaflet = await import('leaflet');

		leaflet.Icon.Default.imagePath = 'leaflet/';

		// Map is initially centered on Göttingen
		map = leaflet.map(mapElement).setView([51.53443, 9.93228], 6);

		leaflet
			.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			})
			.addTo(map);

		//
		// Fetch project data if necessary
		//

		const [count, listData] = await fetchList();

		if (entriesValue.length !== count) {
			const freshEntries = await fetchEntries(listData);
			const freshKeywords = getKeywords(freshEntries);

			entries.set(freshEntries);
			keywordsMap.set(freshKeywords);
		}

		//
		// Generate map of places to projects
		//

		for (const [url, data] of entriesValue) {
			const places = filterPlaces(data.project.places);
			const jsonUrl = `${jsonUrlPrefix}${url.split('/PROJECTS/')[1]}`;

			for (const place of places) {
				if (placesMap[place.place_name.text]) {
					placesMap[place.place_name.text].projects.push({
						title: data.project.title,
						url: jsonUrl
					});
				} else {
					const initial: PlaceData = {
						lat: place.coordinates.lat,
						lng: place.coordinates.lng,
						projects: [{ title: data.project.title, url: jsonUrl }]
					};

					placesMap[place.place_name.text] = initial;
				}
			}
		}

		//
		// Add markers to the map
		//

		for (const [, data] of Object.entries(placesMap)) {
			const joined = data.projects
				.map(
					(project) =>
						`<a href=${project.url} target="_blank" rel="noreferrer">${project.title}</a>`
				)
				.join('<hr class="my-1.5" />');

			leaflet.marker([data.lat, data.lng]).addTo(map).bindPopup(joined, {
				maxHeight: 250,
				maxWidth: 250
			});
		}
	});

	onDestroy(async () => {
		if (map) {
			map.remove();
		}
	});
</script>

<div class="mx-auto max-w-6xl px-4">
	<div bind:this={mapElement} class="h-[32rem] rounded-md font-normal" />
</div>

<style>
	@import 'leaflet/dist/leaflet.css';
</style>
