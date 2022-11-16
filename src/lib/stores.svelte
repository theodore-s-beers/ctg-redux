<script lang="ts" context="module">
	import { writable } from 'svelte/store';
	import type { Writable } from 'svelte/store';

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export const entries: Writable<[string, Record<string, any>][]> = writable([]);
</script>
