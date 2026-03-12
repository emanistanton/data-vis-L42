<script>
  import Scrolly from "svelte-scrolly";
  import projects from "$lib/projects.json";

  let scrollyProgress = 0;

  let sorted_projects = projects.sort((a, b) => a.year - b.year);

  let progressPerProject = 100 / sorted_projects.length;

  $: activeProjectIdx = Math.min(
    sorted_projects.length - 1,
    Math.floor(scrollyProgress / progressPerProject)
  );
</script>

<div class="scrolly-wrapper">
  <Scrolly bind:progress={scrollyProgress}>
    <!-- Narrative / story slides -->
    {#each sorted_projects as project}
      <section class="step">
        <div class="step-content">
          <h3>{project.title}</h3>
          <p>{project.story}</p>
        </div>
      </section>
    {/each}

    <!-- Sticky visualization panel -->
    <svelte:fragment slot="viz">
      <div class="project-detail">
        <div class="project-year">{sorted_projects[activeProjectIdx].year}</div>
        <img
          src={sorted_projects[activeProjectIdx].image}
          alt={sorted_projects[activeProjectIdx].title}
          width="100%"
        />
        <p class="project-title">{sorted_projects[activeProjectIdx].title}</p>
        <p class="project-desc">{sorted_projects[activeProjectIdx].description}</p>
      </div>
    </svelte:fragment>
  </Scrolly>
</div>

<style>
  .scrolly-wrapper {
    width: min(1000ch, 60vw);
    position: relative;
    left: 50%;
    transform: translateX(-50%);
  }

  .step {
    min-height: 80vh;
    padding: 2rem;
    display: flex;
    align-items: center;
  }

  .step-content {
    border-left: 4px solid var(--color-accent, oklch(50% 30% 250));
    padding: 1.5rem 2rem;
    background: color-mix(in oklch, var(--color-accent, oklch(50% 30% 250)), canvas 92%);
    border-radius: 0 0.65rem 0.65rem 0;
  }

  .step-content h3 {
    margin: 0 0 0.65rem;
    font-size: 1.1rem;
    color: var(--color-accent, oklch(50% 30% 250));
  }

  .step-content p {
    margin: 0;
    line-height: 1.7;
    max-width: 48ch;
  }

  .project-detail {
    padding: 2rem;
    width: 100%;
  }

  .project-year {
    font-size: 3rem;
    font-weight: 800;
    color: var(--color-accent, oklch(50% 30% 250));
    line-height: 1;
    margin-bottom: 1rem;
    letter-spacing: -0.02em;
  }

  .project-title {
    font-size: 1rem;
    font-weight: 700;
    margin: 0.75rem 0 0.4rem;
  }

  .project-desc {
    font-size: 0.875rem;
    line-height: 1.6;
    opacity: 0.75;
    margin: 0;
  }
</style>
