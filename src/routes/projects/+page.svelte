<script>
  import { base } from "$app/paths";
  import Project from "$lib/Project.svelte";
  import projects from "$lib/projects.json";
</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<h1>My Projects ({projects.length})</h1>
<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>
