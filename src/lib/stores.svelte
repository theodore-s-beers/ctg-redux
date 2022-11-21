<script lang="ts" context="module">
	import { writable } from 'svelte/store';
	import type { Writable } from 'svelte/store';
	import type { JsonStuff } from '$lib/utils.svelte';

	export const entries: Writable<[string, JsonStuff][]> = writable([]);
	export const categoriesMap: Writable<Record<string, string[]>> = writable({});
	export const keywordsMap: Writable<Record<string, string[]>> = writable({});

	export const selectedTab: Writable<string> = writable('keywords');
	export const selectedTerm: Writable<string> = writable('');
</script>
