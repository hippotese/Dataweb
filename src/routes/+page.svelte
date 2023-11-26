<script lang="ts">
	import * as vg from '@uwdata/vgplot';

	import { load } from '$lib/load';
	import { FilterTime, FilterSignal, Timeseries, Distribution, Table } from '$lib/charts';

	let filelist: FileList | undefined;

	const init = async (filelist: FileList | undefined) => {
		let result;
		let selection;
		if (filelist) {
			const connector = await vg.wasmConnector({ log: false, cache: false });
			// load the files
			result = await load(connector.db, filelist);
			// setup the coordinator
			let coordinator = await vg.coordinator();
			coordinator.databaseConnector(connector);
			coordinator.clear();
			// create the selection
			selection = vg.Selection.crossfilter();
		}
		return { ...result, selection };
	};

	let innerWidth: number = 1000;
</script>

<svelte:window bind:innerWidth />

<menu class="fileloader">
	<input
		accept=".csv, text/plain"
		bind:files={filelist}
		id="files"
		name="files"
		type="file"
		multiple
	/>
</menu>
{#if filelist}
	{#await init(filelist)}
		<span class="info">Charger...</span>
	{:then { metadata, selection }}
		<article>
			<h2>Graphiques</h2>
			<h3>Séries temporelles minimap</h3>
			<p>Faire glisser horizontalement pour zoomer sur une période</p>
			<FilterTime {selection} {innerWidth} />
			<h3>Séries Temporelles</h3>
			<p>Les valeurs d'effort dans le temps</p>
			<Timeseries {selection} {innerWidth} />
			<h3>Distribution de fréquence minimap</h3>
			<p>Faire glisser horizontalement pour zoomer sur une gamme d'efforts</p>
			<FilterSignal {selection} {innerWidth} />
			<h3>Distribution de fréquence</h3>
			<p>Fréquence d'apparition d'une valeur d'effort</p>
			<Distribution {selection} {innerWidth} />
			<h2>Tableaux</h2>
			<h3>Données de séries temporelles</h3>
			<p>Traitée pour la correction du temps et la transformation des données.</p>
			<Table
				from="traction_collated"
				{selection}
				{innerWidth}
				columns={{
					horodatage: 200,
					t: 50,
					capteur_1: 100,
					...(metadata.sensorCount >= 2 ? { capteur_2: 100 } : {}),
					total: 100
				}}
				align={{
					horodatage: 'left',
					t: 'left',
					capteur_1: 'center',
					...(metadata.sensorCount >= 2 ? { capteur_2: 'center' } : {}),
					total: 'center'
				}}
				format={{
					horodatage: (field) =>
						new Date(field).toISOString().replace('T', ' ').replace('Z', '').slice(0, 19)
				}}
			/>
		</article>
	{/await}
{:else}
	<span class="info">Sélectionnez un ou plusieurs fichiers datafficheur pour commencer...</span>
{/if}

<style>
	.fileloader {
		display: flex;
		flex-flow: row;
		justify-content: center;
		align-items: center;
		padding: 0.5rem;
	}
</style>
