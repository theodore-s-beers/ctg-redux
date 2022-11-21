<script lang="ts">
	import type { JsonStuff } from '$lib/utils.svelte';

	export let title: string;
	export let description: string;
	export let startDate: string;
	export let endDate: string;
	export let places: JsonStuff[];
	export let languages: string[];
	export let websiteLink: string;
	export let jsonLink: string;
	export let lastEdited: string;
	export let categories: string[];
	export let keywords: string[];
</script>

<div class="mb-auto w-[21rem] rounded-lg bg-[#b8b08d] p-4 text-lg sm:w-[22rem] lg:w-96">
	<h3 class="mb-2 font-bold">{title}</h3>

	<p class="mb-4">{description}</p>

	<p><strong>Start date:</strong> {startDate}</p>

	<p class="mb-4"><strong>End date:</strong> {endDate}</p>

	<p class="mb-4">
		<strong>Locations:</strong>

		{#if places.length > 0}
			{#each places as place, i}
				{i === 0 ? '' : ', '}
				<a class="hover:underline" href={place.place_name.ref}
					>{place.place_name.text}</a
				>
			{/each}
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>Languages:</strong>

		{#if languages.length > 0}
			{#each languages as language, i}
				{i === 0 ? '' : ', '}
				<code class="text-base">{language}</code>
			{/each}
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>Categories:</strong>

		{#if categories.length > 0}
			{#each categories as category, i}
				{i === 0 ? '' : ', '}
				<code class="text-base">{category}</code>
			{/each}
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>Keywords:</strong>

		{#if keywords.length > 0}
			{#each keywords as keyword, i}
				{i === 0 ? '' : ', '}
				<code class="text-base">{keyword}</code>
			{/each}
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>Website:</strong>

		{#if websiteLink}
			<a class="break-words text-base hover:underline" href={websiteLink}
				><code>{websiteLink}</code></a
			>
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>JSON data:</strong>

		<a class="break-words text-base hover:underline" href={jsonLink}
			><code>{jsonLink}</code></a
		>
	</p>

	<p><strong>Last edited:</strong> <code class="text-base">{lastEdited}</code></p>
</div>
