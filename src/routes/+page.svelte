<script lang="ts">
	import { onMount } from 'svelte';
	import { read, utils, writeFileXLSX } from 'xlsx';
	import ScatterSvg from './_components/Scatter.svg.svelte';
	import AxisX from './_components/AxisX.svg.svelte';
	import AxisY from './_components/AxisY.svg.svelte';

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

	type dataType = {
		[columnName: string]: number;
	};

	const xKey = 'myX';
	const yKey = 'myY';

	let data: dataType[] = [
		{ [xKey]: 1979, [yKey]: 7.19 },
		{ [xKey]: 1980, myY: 7.43 },
		{ [xKey]: 1981, myY: 7.24 },
		{ [xKey]: 1983, myY: 7.44 }
	];

	const r = 3;
	const padding = 10;
	const color = '#fff';

	data.forEach((d) => {
		d[yKey] = +d[yKey];
	});

	function download() {
		const svg = document
			.getElementsByClassName('layercake-layout-svg ')[0]
			.getElementsByTagName('g')[0];

		console.log(svg);
		const blob = new Blob([svg.outerHTML], { type: 'image/svg+xml' });
		const link = document.createElement('a');
		link.href = URL.createObjectURL(blob);
		link.download = `File name test.svg`;
		link.click();
	}
</script>

<main>
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
