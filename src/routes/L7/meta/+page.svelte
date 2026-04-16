<script>
  import { onMount } from 'svelte';
  import { base } from '$app/paths';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';

  let locData = [];
  let langData = [];
  let commits = [];

  const width = 1000;
  const height = 600;
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
    {#each commits as commit}
      <circle
        cx={xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r="5"
        fill="steelblue"
      />
    {/each}
  </g>
</svg>

<h2>Lines of Code by Language</h2>
<BarHorizontal data={langData} />

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
</style>
