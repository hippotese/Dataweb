<script lang="ts">
	import * as vg from '@uwdata/vgplot';
	import { onMount, onDestroy } from 'svelte';

	export let selection: any;
	export let innerWidth: number;

	let el: HTMLElement;
	$: plot = vg.plot(
		vg.areaY(vg.from('traction'), { x: 't', y: 'signal', fill: 'capteur' }),
		vg.width(innerWidth),
		vg.height(120),
		vg.intervalX({ as: selection, field: 't' }),
		vg.yLabel('Effort (Kgf ou daN)'),
		vg.margin(0),
		vg.grid(true),
		vg.colorScale('categorical'),
		vg.colorLegend()
	);

	onMount(() => {
		el.appendChild(plot);
	});
	onDestroy(() => {
		el.removeChild(plot);
	});
</script>

<div class="chart" bind:this={el} />
