<script lang="ts">
	import type { PlotlyHTMLElement } from 'plotly.js-dist';
	import { onMount } from 'svelte';
	import Plot from 'svelte-plotly.js';

	let Plotly: any;

	onMount(async () => {
		Plotly = await import('plotly.js-dist');
	});

	let ele = $state<PlotlyHTMLElement | null>();
	let count = $state(0);
	let data = $state([
		{
			x: [1, 2, 3, 4, 5],
			y: [1, 2, 4, 8, 16]
		}
	]);

	function test() {
		Plotly.downloadImage(ele, {
			format: 'svg',
			width: 800,
			height: 600,
			filename: 'newplot'
		});
	}

	function moreData() {
		data[0].x.push(6 + count);
		data[0].y.push(18 + count);
		count++;
	}
</script>

<Plot
	{data}
	layout={{
		margin: { t: 0 }
	}}
	bind:plot={ele}
	fillParent="width"
	debounce={200}
/>

<button onclick={moreData}>yest</button>
<button onclick={test}>downolad</button>
