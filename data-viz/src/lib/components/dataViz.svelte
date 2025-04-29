<script lang="ts">
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	export let data: {
		age: number;
		is_journalist: boolean;
		years_of_practice: number;
		internet_opinion: boolean;
		internet_opinion_score: number;
		gpt_opinion: boolean;
		gpt_opinion_score: number;
	}[] = [];

	let svgEl: SVGSVGElement;
	const width = 800;
	const height = 600;

	let currentVizIndex = 0;

	const visualizations = [drawAgeVsInternetScore, drawAgeVsGptScore, drawPracticeVsGptScore];

	function retriveData() {
		fetch('http://127.0.0.1:8000/get-interviews')
			.then((res) => res.json())
			.then((response) => {
				data = response['interviews'];
				render();
			})
			.catch((err) => console.error(err));
	}

	onMount(() => {
		retriveData();
	});

	function render() {
		if (data.length === 0) return;
		const svg = d3.select(svgEl);
		svg.selectAll('*').remove();
		visualizations[currentVizIndex](svg);
	}

	function nextViz() {
		currentVizIndex = (currentVizIndex + 1) % visualizations.length;
		render();
	}

	function prevViz() {
		currentVizIndex = (currentVizIndex - 1 + visualizations.length) % visualizations.length;
		render();
	}

	// Visualization 1
	function drawAgeVsInternetScore(svg: d3.selection<SVGSVGElement, unknown, null, undefined>) {
		drawScatter(
			svg,
			'Age',
			'Internet Opinion Score',
			(d) => d.age,
			(d) => d.internet_opinion_score
		);
	}

	// Visualization 2
	function drawAgeVsGptScore(svg: d3.selection<SVGSVGElement, unknown, null, undefined>) {
		drawScatter(
			svg,
			'Age',
			'GPT Opinion Score',
			(d) => d.age,
			(d) => d.gpt_opinion_score
		);
	}

	// Visualization 3
	function drawPracticeVsGptScore(svg: d3.selection<SVGSVGElement, unknown, null, undefined>) {
		drawScatter(
			svg,
			'Years of Practice',
			'GPT Opinion Score',
			(d) => d.years_of_practice,
			(d) => d.gpt_opinion_score
		);
	}

	function drawScatter(
		svg: d3.selection<SVGSVGElement, unknown, null, undefined>,
		xLabel: string,
		yLabel: string,
		xAccessor: (d) => number,
		yAccessor: (d) => number
	) {
		const margin = { top: 30, right: 50, bottom: 50, left: 100 };
		const innerWidth = width - margin.left - margin.right;
		const innerHeight = height - margin.top - margin.bottom;

		const g = svg.append('g').attr('transform', `translate(${margin.left},${margin.top})`);

		const x = d3
			.scaleLinear()
			.domain(d3.extent(data, xAccessor) as [number, number])
			.range([0, innerWidth]);

		const y = d3.scaleLinear().domain([0, 10]).range([innerHeight, 0]);

		g.append('g')
			.attr('transform', `translate(0,${innerHeight})`)
			.call(d3.axisBottom(x).ticks(5))
			.append('text')
			.attr('x', innerWidth / 2)
			.attr('y', 35)
			.attr('fill', '#fff')
			.text(xLabel);

		g.append('g')
			.call(d3.axisLeft(y))
			.append('text')
			.attr('x', -30)
			.attr('y', -10)
			.attr('fill', '#fff')
			.text(yLabel);

		g.selectAll('circle')
			.data(data)
			.join('circle')
			.attr('cx', (d) => x(xAccessor(d)))
			.attr('cy', (d) => y(yAccessor(d)))
			.attr('r', 5)
			.attr('fill', '#81e9c6');
	}
</script>

<div class="controls">
	<button on:click={prevViz}>⟵ Previous</button>
	<button on:click={nextViz}>Next ⟶</button>
</div>

<svg bind:this={svgEl}></svg>

<style>
	svg {
		background: #805a8a;
		border-radius: 12px;
		display: block;
		margin: 2rem auto;
		width: 95%;
		height: 500px;
	}

	text {
		font-size: 0.8rem;
		fill: white;
	}

	.controls {
		text-align: center;
		margin-top: 1rem;
	}

	button {
		background: #81e9c6;
		border: none;
		color: #222;
		padding: 0.5rem 1rem;
		margin: 0 0.5rem;
		border-radius: 8px;
		cursor: pointer;
		font-weight: bold;
		transition: background 0.2s;
	}

	button:hover {
		background: #5fdcb0;
	}
</style>
