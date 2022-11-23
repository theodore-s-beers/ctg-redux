<script lang="ts">
	import type { JsonStuff } from '$lib/utils.svelte';

	import Arrow from '$lib/svg/Arrow.svelte';
	import Calendar from '$lib/svg/Calendar.svelte';
	import CodeLink from '$lib/svg/CodeLink.svelte';
	import Link from '$lib/svg/Link.svelte';
	import Pin from '$lib/svg/Pin.svelte';

	export let title: string;
	export let description: string;
	export let periods: Record<string, string>[];
	export let places: JsonStuff[];
	export let languages: string[];
	export let websiteLinks: string[];
	export let jsonLink: string;
	export let lastEdited: string;
	export let categories: string[];
	export let keywords: string[];

	$: realPeriods = periods.filter((period) => period.from);
</script>

<div class="mb-auto w-[21rem] rounded-lg bg-[#b8b08d] text-base sm:w-[22rem] lg:w-96">
	<div class="p-4 pb-3">
		<div class="mb-3 flex gap-x-1.5">
			<div class="w-6"><Pin /></div>

			{#if places.length > 0}
				<div class="flex flex-wrap gap-x-1.5">
					{#each places as place, i}
						<div>
							<a
								href={place.place_name.ref}
								target="_blank"
								rel="noreferrer"
								class="hover:underline">{place.place_name.text}</a
							>{#if i < places.length - 1},{/if}
						</div>
					{/each}
				</div>
			{:else}
				N/A
			{/if}
		</div>

		<h3 class="mb-3 text-xl font-bold">{title}</h3>

		<div class="mb-3">
			{#each realPeriods as period}
				<div class="mb-1.5 flex items-center gap-1.5">
					<div class="w-7"><Calendar /></div>
					<div class="font-mono">{period.from.slice(0, 4)}</div>
					<div class="w-6"><Arrow /></div>
					<div class="font-mono">{period.to.slice(0, 4)}</div>
				</div>
			{/each}
		</div>

		<p class="mb-3">{description}</p>

		<p class="mb-2">
			<strong>Languages:</strong>

			{#if languages.length > 0}
				{#each languages as language, i}
					{i === 0 ? '' : ', '}
					<code>{language}</code>
				{/each}
			{:else}
				N/A
			{/if}
		</p>

		<p class="mb-2">
			<strong>Categories:</strong>

			{#if categories.length > 0}
				{#each categories as category, i}
					{i === 0 ? '' : ', '}
					<code>{category}</code>
				{/each}
			{:else}
				N/A
			{/if}
		</p>

		<p class="mb-3">
			<strong>Keywords:</strong>

			{#if keywords.length > 0}
				{#each keywords as keyword, i}
					{i === 0 ? '' : ', '}
					<code>{keyword}</code>
				{/each}
			{:else}
				N/A
			{/if}
		</p>

		<div class="flex gap-x-2">
			{#each websiteLinks as link}
				<div class="w-8 rounded-full border border-slate-800 bg-[#f29559] p-1">
					<a href={link} target="_blank" rel="noreferrer" class="hover:text-amber-200"
						><Link /></a
					>
				</div>
			{/each}

			<div class="w-8 rounded-full border border-slate-800 bg-[#f29559] p-1">
				<a href={jsonLink} target="_blank" rel="noreferrer" class="hover:text-amber-200"
					><CodeLink /></a
				>
			</div>
		</div>
	</div>

	<div class="rounded-b-lg bg-slate-800 px-3 py-1">
		<p class="text-right text-sm text-gray-300">
			Last edited: <code>{lastEdited}</code>
		</p>
	</div>
</div>
