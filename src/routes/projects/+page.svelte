<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";

  let years = projects.map(proj => proj.year);
  let range = Math.max(...years) - Math.min(...years);
</script>

<svelte:head>
  <title>Projects — Emani Stanton</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

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
</style>
