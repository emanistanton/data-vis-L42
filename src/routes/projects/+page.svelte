<script>
  import { base } from "$app/paths";
  import Project from "$lib/Project.svelte";
  import projects from "$lib/projects.json";
</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<nav>
  <ul>
    <li><a href={`${base}/`}>Home</a></li>
    <li><a href={`${base}/contact`}>Contact</a></li>
    <li><a href={`${base}/projects`} class="current">Projects</a></li>
    <li><a href={`${base}/resume`}>Resume</a></li>
    <li><a href="https://github.com/emanistanton" target="_blank">GitHub</a></li>
  </ul>
</nav>

<h1>My Projects ({projects.length})</h1>
<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>
