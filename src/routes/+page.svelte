<script lang="ts">
	import { onMount } from 'svelte';
	import { afterNavigate } from '$app/navigation';

	import {
		categoriesMap,
		entries,
		keywordsMap,
		selectedTab,
		selectedTerm
	} from '$lib/stores.svelte';

	import { fetchList, fetchEntries, filterPlaces, handleHash } from '$lib/utils.svelte';
	import type { JsonStuff } from '$lib/utils.svelte';

	import Card from '$lib/Card.svelte';
	import Panel from '$lib/Panel.svelte';

	let entriesValue: [string, JsonStuff][];
	let categoriesMapValue: Record<string, string[]>;
	let keywordsMapValue: Record<string, string[]>;

	let selectedTabValue: string;
	let selectedTermValue: string;

	entries.subscribe((value) => {
		entriesValue = value;
	});

	categoriesMap.subscribe((value) => {
		categoriesMapValue = value;
	});

	keywordsMap.subscribe((value) => {
		keywordsMapValue = value;
	});

	selectedTab.subscribe((value) => {
		selectedTabValue = value;
	});

	selectedTerm.subscribe((value) => {
		selectedTermValue = value;
	});

	$: categories = Object.keys(categoriesMapValue).sort();
	$: keywords = Object.keys(keywordsMapValue).sort();

	function filterEntries(entriesValue: [string, JsonStuff][], selectedTerm: string) {
		if (selectedTerm) {
			if (selectedTabValue === 'categories') {
				if (categoriesMapValue[selectedTerm]) {
					const matches = categoriesMapValue[selectedTerm];
					return entriesValue.filter(([url]) => matches.includes(url));
				}
			}

			if (selectedTabValue === 'keywords') {
				if (keywordsMapValue[selectedTerm]) {
					const matches = keywordsMapValue[selectedTerm];
					return entriesValue.filter(([url]) => matches.includes(url));
				}
			}
		}

		return entriesValue;
	}

	$: filtered = filterEntries(entriesValue, selectedTermValue);

	afterNavigate(handleHash);

	onMount(async () => {
		const [count, listData] = await fetchList();

		if (entriesValue.length !== count) {
			await fetchEntries(listData);
		}
	});
</script>

<svelte:window on:hashchange={handleHash} />

<svelte:head>
	<title>Closing the Gap in Non-Latin Script Data – Projects</title>
</svelte:head>

<div class="mx-auto max-w-[77rem] px-4">
	<Panel {categories} {keywords} />

	<p class="mb-4 text-center text-lg text-gray-50">
		<code>{filtered.length}</code> entries
	</p>

	<div class="flex flex-wrap gap-6">
		{#each filtered as [url, data]}
			<Card
				title={data.project.title}
				description={data.project.project_desc}
				startDate={data.project.date[0].from ? data.project.date[0].from : 'N/A'}
				endDate={data.project.date[0].to ? data.project.date[0].to : 'N/A'}
				places={filterPlaces(data.project.places)}
				languages={data.project.lang}
				websiteLink={data.project.websites[0]}
				jsonLink={url}
				lastEdited={data.record_metadata.record_modified[
					data.record_metadata.record_modified.length - 1
				].record_modified_on
					? data.record_metadata.record_modified[
							data.record_metadata.record_modified.length - 1
					  ].record_modified_on
					: data.record_metadata.record_created_on}
				categories={Object.entries(data.project.topic_relations)
					.filter(([, v]) => v === true)
					.map(([k]) => k)}
				keywords={data.project.keywords}
			/>
		{/each}
	</div>
</div>
