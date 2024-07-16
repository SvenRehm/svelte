<script>
	import { onMount } from 'svelte';
	let data = $state([
		{ col1: 'row 1 column 1', col2: 'row 1 column 2', col3: 'row 1 column 3' },
		{ col1: 'row 2 column 1', col2: 'row 2 column 2', col3: 'row 2 column 3' }
	]);
	let grid;

	onMount(() => {
		// @ts-ignore
		grid = canvasDatagrid({
			parentNode: document.getElementById('gridctr'),
			data: []
		});
		document.body.appendChild(grid);

		grid.data = data;
	});

	function pushData() {
		grid.addColumn({
			defaultValue: function (e) {
				return 'Quux';
			},
			title: 'Bar',
			name: 'bar'
		});

		data.push({ bar: 'row 2 column 3' });
		console.log(grid.data);
		grid.data = data;
	}
</script>

<svelte:head>
	<script src="https://unpkg.com/canvas-datagrid/dist/canvas-datagrid.js"></script>
</svelte:head>
<div id="gridctr"></div>
<button onclick={pushData}>add</button>
