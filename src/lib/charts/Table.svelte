<script lang="ts">
	import * as vg from '@uwdata/vgplot';
	import { onMount, onDestroy, getContext } from 'svelte';

	import type { Table } from 'apache-arrow';

	export let from: string;
	export let columns: {} | undefined = undefined;
	export let format: {} | undefined = undefined;
	export let align: {} | undefined = undefined;
	export let selection: any | undefined = undefined;
	export let innerWidth: number;

	let el: HTMLElement;
	let table: Table;
	$: table = vg.table({
		from,
		width: columns,
		maxWidth: innerWidth,
		height: 600,
		margin: 0,
		...(selection ? { filterBy: selection } : {}),
		...(columns ? { columns: Object.keys(columns) } : {}),
		...(align ? { align } : {}),
		...(format ? { format } : {})
	});

	onMount(() => {
		el.appendChild(table);
	});
	onDestroy(() => {
		el.removeChild(table);
	});
</script>

<div class="chart" bind:this={el} />
