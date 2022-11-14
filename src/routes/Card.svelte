<script lang="ts">
	export let title: string;
	export let description: string;
	export let startDate: string;
	export let endDate: string;
	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export let places: Record<string, any>[];
	export let languages: string[];
	export let websiteLink: string;
	export let jsonLink: string;
	export let lastEdited: string;
	export let categories: string[];
	export let tags: string[];
</script>

<div class="mb-auto max-w-sm rounded-lg bg-[#b8b08d] p-4 text-lg">
	<h3 class="mb-2 font-bold">{title}</h3>

	<p class="mb-4">{description}</p>

	<p><strong>Start date:</strong> {startDate}</p>

	<p class="mb-4"><strong>End date:</strong> {endDate}</p>

	<p class="mb-4">
		<strong>Locations:</strong>
		{#each places as place, i}{i === 0 ? '' : ', '}<a
				class="hover:underline"
				href={place.place_name.ref}>{place.place_name.text}</a
			>{/each}
	</p>

	<p class="mb-4">
		<strong>Languages:</strong>
		{#each languages as language, i}{i === 0 ? '' : ', '}<code>{language}</code>{/each}
	</p>

	<p class="mb-4">
		<strong>Categories:</strong>
		{#each categories as category, i}{i === 0 ? '' : ', '}<code>{category}</code>{/each}
	</p>

	<p class="mb-4">
		<strong>Tags:</strong>
		{#if tags.length > 0}
			{#each tags as tag, i}{i === 0 ? '' : ', '}<code>{tag}</code>{/each}
		{:else}
			N/A
		{/if}
	</p>

	<p class="mb-4">
		<strong>Website:</strong>
		<a class="break-words text-base hover:underline" href={websiteLink}
			><code>{websiteLink}</code></a
		>
	</p>

	<p class="mb-4">
		<strong>JSON data:</strong>
		<a class="break-words text-base hover:underline" href={jsonLink}
			><code>{jsonLink}</code></a
		>
	</p>

	<p><strong>Last edited:</strong> {lastEdited}</p>
</div>
