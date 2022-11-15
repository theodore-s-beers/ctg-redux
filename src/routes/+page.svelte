<script lang="ts">
	import { onMount } from 'svelte';
	import Card from '$lib/Card.svelte';
	import Header from '$lib/Header.svelte';

	interface ProjectListing {
		title: string;
		path: string;
	}

	const prefix =
		'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master';
	let urls: string[] = [];

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	let entries: [string, Record<string, any>][] = [];

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	function sortTitles(a: Record<string, any>, b: Record<string, any>) {
		const aTitle = a.project.title.toLowerCase();
		const bTitle = b.project.title.toLowerCase();

		if (aTitle < bTitle) {
			return -1;
		} else if (aTitle > bTitle) {
			return 1;
		} else return 0;
	}

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	function filterPlaces(places: Record<string, any>[]) {
		return places.filter((place) => place.place_name.text);
	}

	onMount(async () => {
		const listResponse = await fetch(
			'https://raw.githubusercontent.com/M-L-D-H/Closing-The-Gap-In-Non-Latin-Script-Data/master/PROJECTS.json'
		);
		const listData: Record<string, ProjectListing> = await listResponse.json();

		const listEntries: [string, ProjectListing][] = Object.entries(listData);
		for (const [id, details] of listEntries) {
			urls = [...urls, `${prefix}${details.path}${id}.json`];
		}

		for (const url of urls) {
			fetch(url)
				.then((response) => response.json())
				.then((data) => {
					if (data.project) {
						entries = [...entries, [url, data]];
						entries.sort((a, b) => sortTitles(a[1], b[1]));
					}
				});
		}
	});
</script>

<svelte:head>
	<title>Closing the Gap in Non-Latin Script Data – Projects</title>
</svelte:head>

<Header />

<div class="mx-auto max-w-7xl px-4">
	<p class="mb-4 text-center text-lg text-gray-50">
		<code>{entries.length}</code> entries
	</p>

	<div class="flex flex-wrap gap-6">
		{#each entries as [url, data]}
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
