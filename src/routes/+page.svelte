<script lang="ts">
	import { onMount } from 'svelte';
	import { read, utils, writeFileXLSX } from 'xlsx';
	import { RevoGrid } from '@revolist/svelte-datagrid';
	import type { ColumnRegular } from '@revolist/revogrid';

	// This part to makesure revogrid component is loaded and ready
	import { defineCustomElements } from '@revolist/revogrid/loader';
	defineCustomElements();

	const columns = [
		{
			prop: 'name',
			name: 'First'
		},
		{
			prop: 'details',
			name: 'Second'
		}
	];
	const source = [
		{
			name: '1',
			details: 'Item 1'
		},
		{
			name: '2',
			details: 'Item 2'
		}
	];

	let pres: number[] = $state([]);
	let files: FileList = $state(undefined);

	// onMount(async () => {
	// 	const f = await (await fetch('https://docs.sheetjs.com/pres.xlsx')).arrayBuffer();
	// 	const wb = read(f); // parse the array buffer
	// 	const ws = wb.Sheets[wb.SheetNames[0]]; // get the first worksheet
	// 	pres = utils.sheet_to_json(ws); // generate objects and update state
	// });
	//
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
</script>

<main>
	<RevoGrid {source} {columns}></RevoGrid>
	<button onclick={() => download()}>Download SVG</button>

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
	<Sheet {style} {mergeCells} {columns} {data} />
</main>

<style>
	:global(body) {
		background: #222224;
		color: #fff;
	}
	.chart-container {
		width: 80%;
		height: 400px;
		margin: 25px auto;
	}
</style>
