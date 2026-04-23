<script>
  import * as d3 from 'd3';

  export let data = [];

  let width = 400;
  let height = 300;
  let margin = { top: 40, right: 150, bottom: 80, left: 60 };

  $: innerWidth  = width  - margin.left - margin.right;
  $: innerHeight = height - margin.top  - margin.bottom;

  $: xScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerWidth])
    .padding(0.2);

  $: yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value) || 1])
    .range([innerHeight, 0]);

  $: colorScale = d3.scaleOrdinal()
    .domain(data.map(d => d.label))
    .range(d3.quantize(t => d3.interpolateBlues(0.3 + t * 0.65), data.length));

  $: maxBar = d3.greatest(data, d => d.value);

  $: description = `A bar chart showing project counts by year. ${data.map(d => `${d.label}: ${d.value} projects`).join(', ')}.`;

  let selectedIndex = -1;
  let liveText = "";
  let showChart = true;

  function toggleBar(index, event) {
    if (!event.key || event.key === "Enter") {
      selectedIndex = index;
      const d = data[index];
      liveText = `${d.label}: ${d.value} projects selected.`;
    }
  }

  function toggleView() {
    showChart = !showChart;
    liveText = showChart ? "Bar chart view shown." : "Table view shown.";
  }

  let xAxis, yAxis;

  $: if (xAxis && yAxis) {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(
      d3.axisLeft(yScale)
        .tickFormat(d => Number.isInteger(d) ? d : "")
        .tickValues(d3.range(0, d3.max(data, d => d.value) + 1))
    );
  }
</script>

<button
  on:click={toggleView}
  aria-pressed={!showChart}
  aria-label="Toggle between bar chart and table view"
  class="toggle-button">
  {showChart ? 'Show Table' : 'Show Chart'}
</button>

{#if showChart}
  <div class="container">
    <svg
      viewBox="0 0 {width} {height}"
      role="img"
      aria-labelledby="bar-title bar-desc">
      <title id="bar-title">Projects by Year</title>
      <desc id="bar-desc">{description}</desc>

      <text
        x={margin.left + innerWidth / 2}
        y={margin.top / 2}
        text-anchor="middle"
        class="chart-title">
        Projects per Year
      </text>

      <g transform="translate({margin.left}, {margin.top + innerHeight})"
         bind:this={xAxis} />
      <g transform="translate({margin.left}, {margin.top})"
         bind:this={yAxis} />

      <g transform="translate({margin.left}, {margin.top})">
        {#each data as d, index}
          <rect
            x={xScale(d.label)}
            y={yScale(d.value)}
            width={xScale.bandwidth()}
            height={innerHeight - yScale(d.value)}
            fill={colorScale(d.label)}
            stroke="black"
            class:bar-selected={selectedIndex === index}
            class:bar-dimmed={selectedIndex !== -1 && selectedIndex !== index}
            tabindex="0"
            role="button"
            aria-label="{d.label}: {d.value} project{d.value === 1 ? '' : 's'}"
            on:click={e => toggleBar(index, e)}
            on:keyup={e => toggleBar(index, e)}
          />
        {/each}

        <text
          x={innerWidth / 2}
          y={innerHeight + margin.bottom - 10}
          text-anchor="middle"
          class="axis-label">
          Year
        </text>

        <text
          x={-(innerHeight / 2)}
          y={-margin.left + 15}
          text-anchor="middle"
          transform="rotate(-90)"
          class="axis-label">
          Number of Projects
        </text>

        {#if maxBar}
          <rect
            x={xScale(maxBar.label)}
            y={yScale(maxBar.value)}
            width={xScale.bandwidth()}
            height={innerHeight - yScale(maxBar.value)}
            fill="none"
            stroke="currentColor"
            stroke-width="2"
          />
          <line
            x1={xScale(maxBar.label) + xScale.bandwidth()}
            y1={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
            x2={xScale(maxBar.label) + xScale.bandwidth() + 30}
            y2={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
            stroke="currentColor"
            stroke-width="1"
          />
          <text
            x={xScale(maxBar.label) + xScale.bandwidth() + 35}
            y={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
            dominant-baseline="middle"
            class="annotation">
            Year with most projects
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
{:else}
  <table aria-label="Table showing project counts by year" class="data-table">
    <caption>Projects by Year</caption>
    <thead>
      <tr>
        <th id="year-header" scope="col">Year</th>
        <th id="projects-header" scope="col">Projects</th>
      </tr>
    </thead>
    <tbody>
      {#each data as d, i}
        <tr>
          <th id="row-{i}" scope="row">{d.label}</th>
          <td aria-labelledby="row-{i} projects-header">{d.value}</td>
        </tr>
      {/each}
    </tbody>
  </table>
{/if}

<p aria-live="polite" class="sr-only">{liveText}</p>

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
    font-size: 1em;
    font-weight: bold;
    fill: currentColor;
  }

  .axis-label {
    font-size: 0.8em;
    fill: currentColor;
  }

  .annotation {
    font-size: 0.7em;
    fill: currentColor;
    font-style: italic;
  }

  rect {
    transition: 300ms;
    outline: none;
    stroke: black;
    stroke-width: 1;
  }

  /* Selection-based dimming via JS-controlled classes */
  rect.bar-dimmed { opacity: 0.45; }
  rect.bar-selected { opacity: 1; }

  /* Hover-based dimming only when nothing is explicitly selected */
  svg:hover rect:not(:hover):not(.bar-selected):not(.bar-dimmed),
  .container:focus-within rect:not(:focus-visible):not(.bar-selected) {
    opacity: 50%;
  }

  rect:focus-visible {
    stroke: white;
    stroke-width: 2px;
    stroke-dasharray: 4;
  }

  .sr-only {
    position: absolute;
    left: -9999px;
    width: 1px;
    height: 1px;
    overflow: hidden;
  }

  .toggle-button {
    margin-bottom: 1rem;
    padding: 0.4rem 0.9rem;
    font-size: 0.875rem;
    cursor: pointer;
    border: 1px solid currentColor;
    border-radius: 4px;
    background: transparent;
    color: inherit;
  }

  .toggle-button:focus-visible {
    outline: 2px solid currentColor;
    outline-offset: 2px;
  }

  .data-table {
    margin-top: 1rem;
    margin-bottom: 1rem;
    border-collapse: collapse;
    width: 100%;
    max-width: 30em;
  }

  .data-table caption {
    font-weight: bold;
    margin-bottom: 0.5em;
    text-align: left;
  }

  .data-table th,
  .data-table td {
    border: 1px solid color-mix(in oklch, currentColor 25%, transparent);
    padding: 0.5em;
    text-align: left;
  }

  .data-table th {
    background-color: color-mix(in oklch, currentColor 8%, canvas);
  }
</style>
