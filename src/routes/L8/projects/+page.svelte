<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  import Bar from '$lib/Bar.svelte';
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  let years = projects.map(proj => proj.year);
  let range = Math.max(...years) - Math.min(...years);

  $: barData = d3.rollups(projects, v => v.length, d => d.year)
    .map(([year, count]) => ({ label: String(year), value: count }))
    .sort((a, b) => Number(a.label) - Number(b.label));

  let rawData = [];
  let wrangled = [];
  let percentages = [];

  onMount(async () => {
    rawData = await d3.json('/lab6_example.json');

    wrangled = d3.rollups(
      rawData,
      v => d3.sum(v, d => d.lines),
      d => d.language
    );

    const total = d3.sum(rawData, d => d.lines);
    percentages = d3.rollups(
      rawData,
      v => +((d3.sum(v, d => d.lines) / total) * 100).toFixed(1),
      d => d.language
    );
  });
</script>

<svelte:head>
  <title>Projects — Emani Stanton</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

<Bar data={barData} />

<section class="wrangling">
  <h2>Data wrangling result</h2>
  <pre>{JSON.stringify(wrangled, null, 2)}</pre>

  <h2>Percentages</h2>
  <pre>{JSON.stringify(percentages, null, 2)}</pre>
</section>

<p class="intro-text">Scroll down to see a timeline of my projects and how they've contributed to my professional and personal life.</p>

<ProjectNarrative />

<p class="outro">Thanks for scrolling through my project story! Feel free to explore all of the projects at your leisure below.</p>

<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>

<style>
  h1 {
    margin-bottom: 0.5rem;
  }

  .intro-text {
    margin: 0 0 1.5rem;
    opacity: 0.75;
  }

  .outro {
    margin-bottom: 2.5rem;
    font-style: italic;
    opacity: 0.7;
  }

  .wrangling {
    margin-bottom: 2rem;
  }

  .wrangling h2 {
    margin-bottom: 0.4rem;
  }

  .wrangling pre {
    background: color-mix(in oklch, currentColor 8%, canvas);
    border-radius: 6px;
    padding: 0.75rem 1rem;
    font-size: 0.85rem;
    overflow-x: auto;
  }
</style>
