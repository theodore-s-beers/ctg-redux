<script lang="ts" context="module">
	import { entries, keywordsMap } from '$lib/stores.svelte';

	interface Listing {
		title: string;
		path: string;
	}

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export type JsonStuff = Record<string, any>;

	const urlPrefix =
		'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master';

	export async function fetchList(): Promise<[number, Record<string, Listing>]> {
		const listResponse = await fetch(`${urlPrefix}/PROJECTS.json`);

		const listData: Record<string, Listing> = await listResponse.json();
		const count = Object.keys(listData).length;

		return [count, listData];
	}

	export async function fetchEntries(listData: Record<string, Listing>): Promise<void> {
		entries.set([]);
		keywordsMap.set({});

		const listEntries: [string, Listing][] = Object.entries(listData);

		const urls: string[] = [];

		for (const [id, details] of listEntries) {
			urls.push(`${urlPrefix}${details.path}${id}.json`);
		}

		for (const url of urls) {
			fetch(url)
				.then((response) => response.json())
				.then((data) => {
					if (data.project) {
						entries.update((value) => [...value, [url, data]]);
						entries.update((value) => value.sort((a, b) => sortTitles(a[1], b[1])));

						const keywords: string[] = data.project.keywords;

						for (const keyword of keywords) {
							keywordsMap.update((value) => {
								if (value[keyword]) {
									value[keyword].push(url);
								} else {
									value[keyword] = [url];
								}

								return value;
							});
						}
					}
				});
		}
	}

	export function filterPlaces(places: JsonStuff[]): JsonStuff[] {
		return places.filter((place) => place.place_name.text);
	}

	function sortTitles(a: JsonStuff, b: JsonStuff): 1 | -1 | 0 {
		const aTitle = a.project.title.toLowerCase();
		const bTitle = b.project.title.toLowerCase();

		if (aTitle < bTitle) {
			return -1;
		} else if (aTitle > bTitle) {
			return 1;
		} else return 0;
	}
</script>
