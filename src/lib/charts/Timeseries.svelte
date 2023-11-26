<script lang="ts">
	import * as vg from '@uwdata/vgplot';
	import { onMount, onDestroy } from 'svelte';

	export let selection: any;
	export let innerWidth: number;

	let el: HTMLElement;
	$: plot = vg.plot(
		// sensors signal
		vg.line(vg.from('traction', { filterBy: selection }), {
			x: 't',
			y: 'signal',
			stroke: 'capteur',
			opacity: 0.3
		}),
		vg.line(vg.from('traction', { filterBy: selection }), {
			x: 't',
			y: vg.avg('signal').partitionby('capteur').orderby('t').rows([-10, 10]),
			stroke: 'capteur'
		}),
		vg.width(innerWidth),
		vg.height(400),
		vg.xDomain(selection),
		vg.yLabel('Effort (Kgf ou daN)'),
		vg.margin(0),
		vg.grid(true),
		vg.colorScale('categorical')
	);

	onMount(() => {
		el.appendChild(plot);
	});
	onDestroy(() => {
		el.removeChild(plot);
	});
</script>

<div class="chart" bind:this={el} />
