<script>
  import * as d3 from 'd3';

  export let data = [];
  export let title = "";

  let width = 500;
  let height = 220;
  let margin = { top: 40, right: 130, bottom: 50, left: 70 };

  $: innerWidth  = width  - margin.left - margin.right;
  $: innerHeight = height - margin.top  - margin.bottom;

  $: xScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value) || 1])
    .range([0, innerWidth]);

  $: yScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerHeight])
    .padding(0.25);

  $: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
    .domain(data.map(d => d.label));

  $: maxBar = d3.greatest(data, d => d.value);

  let xAxis, yAxis;

  $: if (xAxis && yAxis) {
    let maxVal = d3.max(data, d => d.value) || 1;
    d3.select(xAxis).call(
      d3.axisBottom(xScale)
        .ticks(Math.min(maxVal, 10))
        .tickFormat(d3.format('d'))
    );
    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>

<div class="container">
  <svg viewBox="0 0 {width} {height}">
    <text
      x={margin.left + innerWidth / 2}
      y={margin.top / 2}
      text-anchor="middle"
      class="chart-title">
      {title}
    </text>

    <g transform="translate({margin.left}, {margin.top + innerHeight})"
       bind:this={xAxis} />
    <g transform="translate({margin.left}, {margin.top})"
       bind:this={yAxis} />

    <g transform="translate({margin.left}, {margin.top})">
      {#each data as d}
        <rect
          x={0}
          y={yScale(d.label)}
          width={xScale(d.value)}
          height={yScale.bandwidth()}
          fill={colorScale(d.label)}
        />
      {/each}

      <text
        x={innerWidth / 2}
        y={innerHeight + margin.bottom - 8}
        text-anchor="middle"
        class="axis-label">
        Lines of Code
      </text>

      <text
        x={-(innerHeight / 2)}
        y={-margin.left + 15}
        text-anchor="middle"
        transform="rotate(-90)"
        class="axis-label">
        Language
      </text>

      {#if maxBar}
        <rect
          x={0}
          y={yScale(maxBar.label)}
          width={xScale(maxBar.value)}
          height={yScale.bandwidth()}
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        />
        <text
          x={xScale(maxBar.value) + 5}
          y={yScale(maxBar.label) + yScale.bandwidth() / 2}
          dominant-baseline="middle"
          text-anchor="start"
          class="annotation">
          Most lines
        </text>
      {/if}
    </g>
  </svg>

  <ul class="legend">
    {#each data as d}
      <li style="--color: {colorScale(d.label)}">
        <span class="swatch"></span>
        {d.label} <em>({d.value})</em>
      </li>
    {/each}
  </ul>
</div>

<style>
  svg {
    max-width: 100%;
    height: auto;
    overflow: visible;
  }

  .container {
    display: flex;
    align-items: flex-start;
    gap: 2rem;
  }

  .legend {
    flex: 1;
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
  }

  li {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    font-size: 0.875rem;
  }

  .swatch {
    display: inline-block;
    width: 12px;
    height: 12px;
    border-radius: 2px;
    background-color: var(--color);
    flex-shrink: 0;
  }

  em {
    opacity: 0.6;
    font-style: normal;
  }

  .chart-title {
    font-size: 0.85em;
    font-weight: bold;
    fill: currentColor;
  }

  .axis-label {
    font-size: 0.7em;
    fill: currentColor;
  }

  .annotation {
    font-size: 0.65em;
    fill: currentColor;
    font-style: italic;
  }
</style>
