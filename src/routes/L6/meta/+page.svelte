<script>
  import { onMount } from 'svelte';
  import { base } from '$app/paths';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';

  let locData = [];
  let langData = [];

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line:   Number(row.line),
      length: Number(row.length),
      depth:  Number(row.depth)
    }));

    langData = d3.rollups(locData, v => v.length, d => d.type)
      .map(([label, value]) => ({ label, value }))
      .sort((a, b) => b.value - a.value);
  });
</script>

<svelte:head>
  <title>Meta — Emani Stanton</title>
</svelte:head>

<h1>Meta</h1>
<p class="intro">Code statistics and analysis of this portfolio's codebase, generated from <code>loc.csv</code>.</p>

<h2>Lines of Code by Language</h2>
<BarHorizontal data={langData} />

<style>
  .intro {
    opacity: 0.75;
    margin-bottom: 1.5rem;
  }
</style>
