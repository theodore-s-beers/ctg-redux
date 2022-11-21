<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import { page } from '$app/stores';

	import { categoriesMap, entries, keywordsMap } from '$lib/stores.svelte';
	import { fetchList, fetchEntries, filterPlaces } from '$lib/utils.svelte';
	import type { JsonStuff } from '$lib/utils.svelte';

	import Card from '$lib/Card.svelte';
	import Panel from '$lib/Panel.svelte';

	let entriesValue: [string, JsonStuff][];
	let categoriesMapValue: Record<string, string[]>;
	let keywordsMapValue: Record<string, string[]>;

	entries.subscribe((value) => {
		entriesValue = value;
	});

	categoriesMap.subscribe((value) => {
		categoriesMapValue = value;
	});

	keywordsMap.subscribe((value) => {
		keywordsMapValue = value;
	});

	$: categories = Object.keys(categoriesMapValue).sort();
	$: keywords = Object.keys(keywordsMapValue).sort();

	onMount(async () => {
		const [count, listData] = await fetchList();

		if (entriesValue.length !== count) {
			await fetchEntries(listData);
		}

		if (browser && $page.url.hash) {
			console.log(`Hash: ${$page.url.hash}`);
		}
	});
</script>

<svelte:head>
	<title>Closing the Gap in Non-Latin Script Data – Projects</title>
</svelte:head>

<div class="mx-auto max-w-[77rem] px-4">
	<Panel {categories} {keywords} selected="search" />

	<p class="mb-4 text-center text-lg text-gray-50">
		<code>{entriesValue.length}</code> entries
	</p>

	<div class="flex flex-wrap justify-between gap-y-6">
		{#each entriesValue as [url, data]}
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
