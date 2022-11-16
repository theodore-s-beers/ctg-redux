<script lang="ts" context="module">
	import { entries } from '$lib/stores.svelte';

	interface ProjectListing {
		title: string;
		path: string;
	}

	export async function fetchEntries() {
		const listResponse = await fetch(
			'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master/PROJECTS.json'
		);
		const listData: Record<string, ProjectListing> = await listResponse.json();
		const listEntries: [string, ProjectListing][] = Object.entries(listData);

		const prefix =
			'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master';
		const urls: string[] = [];

		for (const [id, details] of listEntries) {
			urls.push(`${prefix}${details.path}${id}.json`);
		}

		for (const url of urls) {
			fetch(url)
				.then((response) => response.json())
				.then((data) => {
					if (data.project) {
						entries.update((value) => [...value, [url, data]]);
						entries.update((value) => value.sort((a, b) => sortTitles(a[1], b[1])));
					}
				});
		}
	}

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export function filterPlaces(places: Record<string, any>[]) {
		return places.filter((place) => place.place_name.text);
	}

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export function sortTitles(a: Record<string, any>, b: Record<string, any>) {
		const aTitle = a.project.title.toLowerCase();
		const bTitle = b.project.title.toLowerCase();

		if (aTitle < bTitle) {
			return -1;
		} else if (aTitle > bTitle) {
			return 1;
		} else return 0;
	}
</script>
