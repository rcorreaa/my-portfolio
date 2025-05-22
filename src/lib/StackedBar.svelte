<script>
  import * as d3 from "d3";
  export let data = {};
  export let width = 800;
  export let height = 50;
  export let colorScale = d3.scaleOrdinal(d3.schemeTableau10);

  const margin = { top: 10, right: 20, bottom: 20, left: 20 };
  const barHeight = height - margin.top - margin.bottom;
  const MIN_LABEL_WIDTH = 40;

  $: keys = Object.keys(data);
  $: dataForStack = [Object.fromEntries(data)];
  $: stackedData = d3.stack().keys(keys)(dataForStack);
  $: total = d3.max(stackedData, series => d3.max(series, d => d[1])) || 1;
  $: xScale = d3.scaleLinear().domain([0, total]).range([margin.left, width - margin.right]);
</script>

<svg {width} height={height}>
  {#each stackedData as series (series.key)}
    {#each series as d}
      <rect
        x={xScale(d[0])}
        y={margin.top}
        width={xScale(d[1]) - xScale(d[0])}
        height={barHeight}
        fill={colorScale(series.key)}
      />
      {#if (xScale(d[1]) - xScale(d[0]) > MIN_LABEL_WIDTH)}
        <text
          x={(xScale(d[0]) + xScale(d[1])) / 2}
          y={margin.top + barHeight / 2}
          text-anchor="middle"
          fill="white"
          dominant-baseline="middle"
        >
          {series.key}: {d[1] - d[0]}
        </text>
      {/if}
    {/each}
  {/each}
</svg>
