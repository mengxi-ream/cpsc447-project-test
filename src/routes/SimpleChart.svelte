<script lang="ts">
	import * as d3 from 'd3';

	// 示例数据
	const data = [
		{ label: 'A', value: 10 },
		{ label: 'B', value: 20 },
		{ label: 'C', value: 15 },
		{ label: 'D', value: 25 },
		{ label: 'E', value: 18 }
	];

	// 图表尺寸和边距
	const width = 400;
	const height = 300;
	const margin = { top: 20, right: 20, bottom: 30, left: 40 };

	// 计算比例尺
	$: x = d3
		.scaleBand()
		.domain(data.map((d) => d.label))
		.range([margin.left, width - margin.right])
		.padding(0.1);

	$: y = d3
		.scaleLinear()
		.domain([0, d3.max(data, (d) => d.value) || 0])
		.nice()
		.range([height - margin.bottom, margin.top]);

	// 创建坐标轴
	$: xAxis = (g: SVGGElement) => {
		d3.select(g)
			.attr('transform', `translate(0,${height - margin.bottom})`)
			.call(d3.axisBottom(x));
	};

	$: yAxis = (g: SVGGElement) => {
		d3.select(g).attr('transform', `translate(${margin.left},0)`).call(d3.axisLeft(y));
	};
</script>

<div class="chart-container">
	<h1>D3 Bar Chart</h1>
	<div id="chart">
		<svg {width} {height}>
			<!-- 绘制条形 -->
			{#each data as d}
				<rect
					x={x(d.label)}
					y={y(d.value)}
					width={x.bandwidth()}
					height={y(0) - y(d.value)}
					fill="var(--color-theme-1)"
				/>
			{/each}

			<!-- 绘制坐标轴 -->
			<g use:xAxis />
			<g use:yAxis />
		</svg>
	</div>
</div>

<style>
	.chart-container {
		padding: 2rem;
		max-width: 800px;
		margin: 0 auto;
	}

	h1 {
		text-align: center;
		color: var(--color-theme-1);
		margin-bottom: 2rem;
	}

	#chart {
		background-color: white;
		border-radius: 8px;
		padding: 1rem;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	:global(#chart rect:hover) {
		opacity: 0.8;
		transition: opacity 0.2s;
	}

	:global(#chart .tick text) {
		font-size: 12px;
	}

	:global(#chart .tick line) {
		stroke: #ddd;
	}

	:global(#chart path.domain) {
		stroke: #ddd;
	}
</style>
