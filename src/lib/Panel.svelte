<script lang="ts">
	export let categories: string[];
	export let keywords: string[];
	export let selected: string;
</script>

<div class="mb-6 rounded-lg bg-[#b8b08d] p-4">
	<div class="mb-4 border-b border-b-[#2e4a61]">
		<ul class="flex gap-1.5 text-lg">
			<li
				on:click={() => {
					selected = 'search';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selected = 'search';
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selected === 'search'}
				class:border-[#2e4a61]={selected === 'search'}
				class:border-[#b8b08d]={selected !== 'search'}
			>
				Search
			</li>
			<li
				on:click={() => {
					selected = 'categories';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selected = 'categories';
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selected === 'categories'}
				class:border-[#2e4a61]={selected === 'categories'}
				class:border-[#b8b08d]={selected !== 'categories'}
			>
				Categories
			</li>
			<li
				on:click={() => {
					selected = 'keywords';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter') {
						selected = 'keywords';
					}
				}}
				class="cursor-pointer rounded-t-md border-x border-t px-2.5 py-1 hover:border-[#2e4a61] hover:bg-[#f29559]"
				class:bg-[#f29559]={selected === 'keywords'}
				class:border-[#2e4a61]={selected === 'keywords'}
				class:border-[#b8b08d]={selected !== 'keywords'}
			>
				Keywords
			</li>
		</ul>
	</div>

	{#if selected !== 'search'}
		<div class="flex flex-wrap gap-2 text-sm">
			{#if selected === 'categories'}
				{#each categories as category}
					<code
						class="cursor-pointer rounded-md border border-[#2e4a61] py-1 px-2 hover:bg-[#2e4a61] hover:text-gray-50"
						>{category}</code
					>
				{/each}
			{:else}
				{#each keywords as keyword}
					<code
						class="cursor-pointer rounded-md border border-[#2e4a61] py-1 px-2 hover:bg-[#2e4a61] hover:text-gray-50"
						>{keyword}</code
					>
				{/each}
			{/if}
		</div>
	{:else}
		<p><em>To be implemented…</em></p>
	{/if}
</div>
