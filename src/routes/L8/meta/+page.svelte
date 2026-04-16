<script>
  import { onMount } from 'svelte';
  import { base } from '$app/paths';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';
  import { computePosition, autoPlacement, offset } from '@floating-ui/dom';

  let locData = [];
  let langData = [];
  let commits = [];
  let barData = [];

  const width = 1000;
  const height = 350;
  const margin = { top: 10, right: 10, bottom: 30, left: 20 };
  const usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left,
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let xAxis, yAxis, yAxisGridlines;
  let hoveredIndex = -1;
  let commitTooltip;
  let tooltipPosition = { x: 0, y: 0 };
  let clickedCommits = [];

  $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

  $: {
    let filteredLines = clickedCommits.length > 0
      ? clickedCommits.flatMap(c => c.lines)
      : locData;
    let langCounts = d3.rollup(filteredLines, v => v.length, d => d.type);
    barData = langData.map(({ label }) => ({
      label,
      value: langCounts.get(label) ?? 0,
    }));
  }

  $: rScale = d3.scaleSqrt()
    .domain(d3.extent(commits, d => d.totalLines))
    .range([5, 30]);

  $: minDate = d3.min(commits, d => d.datetime);
  $: maxDate = (() => {
    let m = d3.max(commits, d => d.datetime);
    if (!m) return undefined;
    let d = new Date(m);
    d.setDate(d.getDate() + 1);
    return d;
  })();

  $: xScale = d3.scaleTime()
    .domain([minDate, maxDate])
    .range([usableArea.left, usableArea.right])
    .nice();

  $: yScale = d3.scaleLinear()
    .domain([0, 24])
    .range([usableArea.bottom, usableArea.top]);

  $: {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(
      d3.axisLeft(yScale)
        .tickFormat(d => String(d % 24).padStart(2, "0") + ":00")
    );
    d3.select(yAxisGridlines).call(
      d3.axisLeft(yScale)
        .tickFormat("")
        .tickSize(-usableArea.width)
    );
  }

  async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;
    if (evt.type === "mouseenter") {
      hoveredIndex = index;
      tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
        strategy: "fixed",
        middleware: [offset(5), autoPlacement()],
      });
    } else if (evt.type === "mouseleave") {
      hoveredIndex = -1;
    } else if (evt.type === "click") {
      let commit = commits[index];
      if (!clickedCommits.includes(commit)) {
        clickedCommits = [...clickedCommits, commit];
      } else {
        clickedCommits = clickedCommits.filter(c => c !== commit);
      }
    }
  }

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line:     Number(row.line),
      depth:    Number(row.depth),
      length:   Number(row.length),
      date:     new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime),
    }));

    langData = d3.rollups(locData, v => v.length, d => d.type)
      .map(([label, value]) => ({ label, value }))
      .sort((a, b) => b.value - a.value);

    commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let { author, date, time, timezone, datetime } = first;
      return {
        id: commit,
        url: "https://github.com/emanistanton/data-vis-L42/commit/" + commit,
        author, date, time, timezone, datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length,
        lines,
      };
    });

    commits = d3.sort(commits, d => -d.totalLines);
  });
</script>

<svelte:head>
  <title>Meta — Emani Stanton</title>
</svelte:head>

<h1>Meta</h1>
<p class="intro">Code statistics and analysis of this portfolio's codebase, generated from <code>loc.csv</code>.</p>

<h2>Commits by Time of Day</h2>
<svg viewBox="0 0 {width} {height}" style="overflow: visible;">
  <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
  <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
  <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
  <g class="dots">
    {#each commits as commit, index}
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
      <circle
        cx={xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r={rScale(commit.totalLines)}
        fill="steelblue"
        fill-opacity="0.6"
        role="img"
        aria-label="Commit by {commit.author} on {commit.datetime?.toLocaleDateString()}"
        class:selected={clickedCommits.includes(commit)}
        on:mouseenter={evt => dotInteraction(index, evt)}
        on:mouseleave={evt => dotInteraction(index, evt)}
        on:click={evt => dotInteraction(index, evt)}
      />
    {/each}
  </g>
</svg>

<dl
  class="info tooltip"
  hidden={hoveredIndex === -1}
  bind:this={commitTooltip}
  style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px"
>
  <dt>Commit</dt>
  <dd><a href={hoveredCommit.url} target="_blank">{hoveredCommit.id}</a></dd>

  <dt>Date</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", { dateStyle: "full" })}</dd>

  <dt>Time</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", { timeStyle: "short" })}</dd>

  <dt>Author</dt>
  <dd>{hoveredCommit.author}</dd>

  <dt>Lines edited</dt>
  <dd>{hoveredCommit.totalLines}</dd>
</dl>

<h2>Lines of Code by Language</h2>
<BarHorizontal
  data={barData}
  title={clickedCommits.length > 0 ? "Selected Commits Breakdown" : "Website Breakdown"}
/>

<style>
  .intro {
    opacity: 0.75;
    margin-bottom: 1.5rem;
  }

  svg {
    width: 100%;
    height: auto;
    overflow: visible;
  }

  .gridlines {
    stroke-opacity: 0.2;
  }

  circle {
    transition: 200ms;
  }

  circle:hover {
    fill: darkgreen;
  }

  circle.selected {
    fill: var(--color-accent);
  }

  dl.info {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 0.25em 1em;
    margin: 0;
    transition-duration: 500ms;
    transition-property: opacity, visibility;
  }

  dl.info dt {
    font-size: 0.75em;
    font-weight: 600;
    text-transform: uppercase;
    opacity: 0.6;
  }

  dl.info dd {
    margin: 0;
  }

  dl.info[hidden]:not(:hover, :focus-within) {
    opacity: 0;
    visibility: hidden;
  }

  .tooltip {
    position: fixed;
    background: oklch(100% 0% 0 / 80%);
    color: black;
    backdrop-filter: blur(6px);
    box-shadow: 0 4px 16px oklch(0% 0% 0 / 20%);
    border-radius: 0.5em;
    padding: 0.75em 1em;
  }
</style>
