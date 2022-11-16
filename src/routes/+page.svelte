<script lang="ts">
	import { onMount } from 'svelte';
	import { entries } from '$lib/stores.svelte';
	import { fetchList, fetchEntries, filterPlaces } from '$lib/utils.svelte';
	import type { JsonStuff } from '$lib/utils.svelte';
	import Card from '$lib/Card.svelte';

	let entriesValue: [string, JsonStuff][];

	entries.subscribe((value) => {
		entriesValue = value;
	});

	onMount(async () => {
		const [count, listData] = await fetchList();

		if (entriesValue.length !== count) {
			await fetchEntries(listData);
		}
	});
</script>

<svelte:head>
	<title>Closing the Gap in Non-Latin Script Data – Projects</title>
</svelte:head>

<div class="mx-auto max-w-7xl px-4">
	<p class="mb-4 text-center text-lg text-gray-50">
		<code>{entriesValue.length}</code> entries
	</p>

	<div class="flex flex-wrap gap-6">
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
				tags={data.project.keywords}
			/>
		{/each}
	</div>
</div>
