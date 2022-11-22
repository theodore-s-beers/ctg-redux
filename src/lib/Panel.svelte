<script lang="ts">
	import { selectedTab, selectedTerm } from '$lib/stores.svelte';
	import { resetHash, setHash } from '$lib/utils.svelte';

	export let categories: string[];
	export let keywords: string[];

	let selectedTabValue: string;
	let selectedTermValue: string;

	selectedTab.subscribe((value) => {
		selectedTabValue = value;
	});

	selectedTerm.subscribe((value) => {
		selectedTermValue = value;
	});

	function validate(
		categories: string[],
		keywords: string[],
		selectedTermValue: string
	) {
		if (selectedTabValue === 'categories') {
			return categories.includes(selectedTermValue);
		}

		if (selectedTabValue === 'keywords') {
			return keywords.includes(selectedTermValue);
		}

		return false;
	}

	$: validSelection = validate(categories, keywords, selectedTermValue);
</script>

<div class="mb-6 rounded-lg bg-[#b8b08d] p-4">
	<div class="mb-4 border-b border-b-[#2e4a61]">
		<ul class="flex gap-1.5 text-lg">
			<li
				on:click={() => {
					selectedTab.set('search');
					resetHash();
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selectedTab.set('search');
						resetHash();
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selectedTabValue === 'search'}
				class:border-[#2e4a61]={selectedTabValue === 'search'}
				class:border-[#b8b08d]={selectedTabValue !== 'search'}
			>
				Search
			</li>

			<li
				on:click={() => {
					selectedTab.set('categories');
					resetHash();
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selectedTab.set('categories');
						resetHash();
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selectedTabValue === 'categories'}
				class:border-[#2e4a61]={selectedTabValue === 'categories'}
				class:border-[#b8b08d]={selectedTabValue !== 'categories'}
			>
				Categories
			</li>

			<li
				on:click={() => {
					selectedTab.set('keywords');
					resetHash();
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selectedTab.set('keywords');
						resetHash();
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selectedTabValue === 'keywords'}
				class:border-[#2e4a61]={selectedTabValue === 'keywords'}
				class:border-[#b8b08d]={selectedTabValue !== 'keywords'}
			>
				Keywords
			</li>
		</ul>
	</div>

	{#if selectedTabValue !== 'search'}
		{#if validSelection}
			<p class="mb-4 text-rose-900 underline">
				<span
					on:click={() => {
						resetHash();
					}}
					on:keydown={(e) => {
						if (e.key === 'Enter') {
							resetHash();
						}
					}}
					class="cursor-pointer">Clear selection</span
				>
			</p>
		{/if}

		<div class="flex flex-wrap gap-2 text-sm">
			{#if selectedTabValue === 'categories'}
				{#each categories as category}
					<code
						on:click={() => {
							setHash('category', category);
						}}
						on:keydown={(e) => {
							if (e.key === 'Enter') {
								setHash('category', category);
							}
						}}
						class="cursor-pointer rounded-md border border-[#2e4a61] py-1 px-2 hover:bg-[#2e4a61] hover:text-gray-50"
						class:bg-[#2e4a61]={selectedTermValue === category}
						class:text-gray-50={selectedTermValue === category}>{category}</code
					>
				{/each}
			{:else}
				{#each keywords as keyword}
					<code
						on:click={() => {
							setHash('keyword', keyword);
						}}
						on:keydown={(e) => {
							if (e.key === 'Enter') {
								setHash('keyword', keyword);
							}
						}}
						class="cursor-pointer rounded-md border border-[#2e4a61] py-1 px-2 hover:bg-[#2e4a61] hover:text-gray-50"
						class:bg-[#2e4a61]={selectedTermValue === keyword}
						class:text-gray-50={selectedTermValue === keyword}>{keyword}</code
					>
				{/each}
			{/if}
		</div>
	{:else}
		<p><em>To be implemented…</em></p>
	{/if}
</div>
