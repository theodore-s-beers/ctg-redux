<script lang="ts" context="module">
	import { browser } from '$app/environment';

	import {
		categoriesMap,
		entries,
		keywordsMap,
		searchTerm,
		selectedTab,
		selectedTerm
	} from '$lib/stores.svelte';

	interface Listing {
		title: string;
		path: string;
	}

	const urlPrefix =
		'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master';

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export type JsonStuff = Record<string, any>;

	export async function fetchEntries(listData: Record<string, Listing>): Promise<void> {
		entries.set([]);
		categoriesMap.set({});
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

						const categories: Record<string, boolean> = data.project.topic_relations;
						const keywords: string[] = data.project.keywords;

						for (const [key, value] of Object.entries(categories)) {
							if (value === true) {
								categoriesMap.update((value) => {
									if (value[key]) {
										value[key].push(url);
									} else {
										value[key] = [url];
									}

									return value;
								});
							}
						}

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

	export async function fetchList(): Promise<[number, Record<string, Listing>]> {
		const listResponse = await fetch(`${urlPrefix}/PROJECTS.json`);

		const listData: Record<string, Listing> = await listResponse.json();
		const count = Object.keys(listData).length;

		return [count, listData];
	}

	export function filterPlaces(places: JsonStuff[]): JsonStuff[] {
		return places.filter((place) => place.place_name.text);
	}

	export function handleHash() {
		if (browser) {
			if (window.location.hash) {
				if (window.location.hash.startsWith('#keyword=')) {
					selectedTab.set('keywords');
					selectedTerm.set(window.location.hash.split('keyword=')[1]);
				} else if (window.location.hash.startsWith('#category=')) {
					selectedTab.set('categories');
					selectedTerm.set(window.location.hash.split('category=')[1]);
				} else {
					// TODO: handle search hash
					// For now, just reset
					searchTerm.set('');
					selectedTerm.set('');
				}
			} else {
				// No hash; just make sure everything is reset
				searchTerm.set('');
				selectedTerm.set('');
			}
		}
	}

	export function resetHash() {
		searchTerm.set('');
		selectedTerm.set('');

		if (browser) {
			window.location.hash = '';
		}
	}

	export function searchEntries(
		entries: [string, JsonStuff][],
		term: string
	): [string, JsonStuff][] {
		const termLower = term.toLowerCase();
		const matches: [string, JsonStuff][] = [];

		for (const [url, entry] of entries) {
			// Title
			const title = entry.project.title.toLowerCase();
			if (title.includes(termLower)) {
				matches.push([url, entry]);
				continue;
			}

			// Abbreviation
			const abbr = entry.project.abbr.toLowerCase();
			if (abbr.includes(termLower)) {
				matches.push([url, entry]);
				continue;
			}

			// Description
			const description = entry.project.project_desc.toLowerCase();
			if (description.includes(termLower)) {
				matches.push([url, entry]);
				continue;
			}

			// Keywords
			const keywords = entry.project.keywords.join(' ');
			if (keywords.includes(termLower)) {
				matches.push([url, entry]);
				continue;
			}

			// Categories
			const categories = Object.entries(entry.project.topic_relations)
				.filter(([, v]) => v === true)
				.map(([k]) => k)
				.join(' ');
			if (categories.includes(termLower)) {
				matches.push([url, entry]);
				continue;
			}

			// Places
			let places = '';
			for (const place of entry.project.places) {
				if (place.place_name.text) {
					places += place.place_name.text.toLowerCase();
					places += ' ';
				}
			}
			places = places.trim();
			if (places.includes(termLower)) {
				matches.push([url, entry]);
				continue; // Not needed at the moment, but maybe later
			}
		}

		return matches;
	}

	export function setHash(type: string, term: string) {
		selectedTerm.set(term);

		if (browser) {
			window.location.hash = `#${type}=${term}`;
		}
	}

	function sortTitles(a: JsonStuff, b: JsonStuff): 1 | -1 | 0 {
		const aTitle = a.project.title.toLowerCase();
		const bTitle = b.project.title.toLowerCase();

		if (aTitle > bTitle) {
			return 1;
		} else if (aTitle < bTitle) {
			return -1;
		} else return 0;
	}
</script>
