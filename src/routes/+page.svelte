<script lang="ts">
	import { onMount } from 'svelte';
	import { read, utils, writeFileXLSX } from 'xlsx';
	import { chartRender } from '$lib/chartRender';
	import { BarData } from '$lib/data/chartData';

	type Column = {
		Name: string;
		Index: number;
	};

	let pres: number[] = $state([]);
	let files: FileList = $state(undefined);

	// onMount(async () => {
	// 	const f = await (await fetch('https://docs.sheetjs.com/pres.xlsx')).arrayBuffer();
	// 	const wb = read(f); // parse the array buffer
	// 	const ws = wb.Sheets[wb.SheetNames[0]]; // get the first worksheet
	// 	pres = utils.sheet_to_json(ws); // generate objects and update state
	// });
	function exportFile() {
		const ws = utils.json_to_sheet(pres);
		const wb = utils.book_new();
		utils.book_append_sheet(wb, ws, 'Data');
		writeFileXLSX(wb, 'SheetJSSvelteAoO.xlsx');
	}

	async function handleDropAsync(e: any) {
		const file = e.target.files[0];
		const data = await file.arrayBuffer();
		/* data is an ArrayBuffer */
		const wb = read(data);
		const ws = wb.Sheets[wb.SheetNames[0]]; // get the first worksheet

		pres = [];
		const test: Column[] = utils.sheet_to_json(ws); // generate objects and update state
		console.log(test);

		pres = test.map((element: Column) => {
			return element.Index;
		});

		console.log(pres);
		console.log(ws);
	}
	// let BarData = $state({
	// 	type: 'bar',
	// 	data: {
	// 		labels: ['January', 'February', 'March', 'April', 'May'],
	// 		datasets: [
	// 			{
	// 				label: 'Bar Dataset',
	// 				data: [55, 20, 30, 10, 85],
	// 				backgroundColor: ['rgba(245, 40, 145, 0.8)']
	// 			}
	// 		]
	// 	},
	// 	options: {
	// 		maintainAspectRatio: false
	// 	}
	// });
	let testY = $state(0);
	let testX = $state(0);
	let Scatter = $derived({
		type: 'scatter',
		data: {
			datasets: [
				{
					label: 'Scatter Dataset',
					data: [
						{
							x: testX,
							y: testY
						},
						{
							x: 0,
							y: 10
						},
						{
							x: 10,
							y: 5
						},
						{
							x: 0.5,
							y: 5.5
						}
					],
					backgroundColor: 'rgb(255, 99, 132)'
				}
			],
			options: {
				scales: {
					x: {
						type: 'linear',
						position: 'bottom'
					}
				}
			}
		}
	});
</script>

<main>
	<div class="w-2/5 h-44 border m-auto">
		<canvas use:chartRender={Scatter}></canvas>
		<canvas use:chartRender={BarData}></canvas>
	</div>

	<label for="test">x</label>
	<input id="test" type="range" bind:value={testY} />

	<label for="y">x</label>
	<input id="y" type="range" bind:value={testX} />

	<pre>{JSON.stringify(pres, null, 2)}</pre>
	<label for="xml">Upload XML</label>
	<input
		accept=".xlsx, .xls, .csv"
		bind:files
		onchange={handleDropAsync}
		id="xml"
		name="xml"
		type="file"
	/>

	<button class="btn btn-secondary">Hello daisyUI</button>

	{#if files}
		<h2>Selected files:</h2>
		{#each Array.from(files) as file}
			<p>{file.name} ({file.size} bytes)</p>
		{/each}
	{/if}

	{#if pres.length > 0}
		<table>
			<thead><tr><th>Name</th><th>Index</th></tr></thead><tbody>
				{#each pres as p}<tr>
						<td>{p.Name}</td>
						<td>{p.Index}</td>
					</tr>{/each}
			</tbody>
			<tfoot> </tfoot>
		</table>
		<button onclick={exportFile}>Export XLSX</button>
	{/if}
</main>

<style>
	:global(body) {
		background: #222224;
		color: #fff;
	}
</style>
