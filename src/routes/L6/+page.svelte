<script>
  import { base } from "$app/paths";
  import { onMount } from "svelte";
  import Project from "$lib/Project.svelte";
  import projects from "$lib/projects.json";
  import ReadingItem from "$lib/ReadingItem.svelte";
  import reading from "$lib/reading.json";

  let githubData = null;
  let loading = true;
  let error = null;

  onMount(async () => {
    try {
      let response = await fetch("https://api.github.com/users/emanistanton");
      githubData = await response.json();
    } catch (err) {
      error = err;
    }
    loading = false;
  });
</script>

<svelte:head>
  <title>Emani Stanton: Personal site and portfolio</title>
</svelte:head>

<main class="home">
  <section class="intro">
    <div class="intro-copy">
      <h1>Emani Stanton</h1>
      <p>
        Hello! I am a senior at MIT, and I'm from Atlanta, Georgia. I'm studying computer science and will
        soon move to New York City to begin working as a quantitative developer at Point72. In my free
        time, I like watching TV shows and basketball. I also enjoy deep-diving into visual storytelling,
        writing, and coaching tactics in sports. I have two sisters and two brothers, so I grew up in a
        pretty big family.
      </p>

      <div class="quick-links">
        <a href={`${base}/L6/projects`}>View Projects</a>
        <a href={`${base}/L6/resume`}>View Resume</a>
      </div>
    </div>

    <figure class="intro-image">
      <img
        src="{base}/images/xena.png"
        alt="DVD cover for Xena: Warrior Princess, a 1990s TV show (this is my favorite TV show)"
      />
      <figcaption>Current comfort show: Xena: Warrior Princess</figcaption>
    </figure>
  </section>

  <section class="section-card github-section">
    <h2>GitHub Stats</h2>
    {#if loading}
      <p class="github-loading">Loading…</p>
    {:else if error}
      <p class="github-error">Something went wrong: {error.message}</p>
    {:else}
      <dl class="github-stats">
        <dt>Followers</dt>
        <dd>{githubData.followers}</dd>
        <dt>Following</dt>
        <dd>{githubData.following}</dd>
        <dt>Public Repos</dt>
        <dd>{githubData.public_repos}</dd>
        <dt>Public Gists</dt>
        <dd>{githubData.public_gists}</dd>
      </dl>
    {/if}
  </section>

  <section class="section-card">
    <h2>What I'm Reading</h2>
    <div class="reading">
      {#each reading as item}
        <ReadingItem data={item} />
      {/each}
    </div>
  </section>

  <section class="section-card">
    <div class="section-header">
      <h2>Latest Projects</h2>
      <a href={`${base}/L6/projects`}>See all {projects.length}</a>
    </div>

    <div class="projects highlights">
      {#each projects.slice(0, 3) as p}
        <Project data={p} />
      {/each}
    </div>
  </section>
</main>

<style>
  .home {
    display: grid;
    gap: 1.6rem;
  }

  .intro {
    display: grid;
    grid-template-columns: minmax(0, 1.35fr) minmax(220px, 1fr);
    gap: 1.4rem;
    align-items: center;
    padding: 1.1rem;
    border: 1px solid var(--color-border);
    border-radius: 0.85rem;
  }

  .intro-copy h1 {
    margin: 0 0 0.8rem;
  }

  .intro-copy p {
    margin: 0;
    max-width: 68ch;
  }

  .quick-links {
    display: flex;
    flex-wrap: wrap;
    gap: 0.65rem;
    margin-top: 1rem;
  }

  .quick-links a {
    text-decoration: none;
    font-weight: 600;
    padding: 0.45rem 0.85rem;
    border: 1px solid var(--color-border);
    border-radius: 999px;
  }

  .intro-image {
    margin: 0;
    justify-self: center;
    max-width: 280px;
  }

  .intro-image img {
    display: block;
    width: 100%;
    border-radius: 0.85rem;
    border: 1px solid var(--color-border);
  }

  .intro-image figcaption {
    margin-top: 0.55rem;
    font-size: 0.9rem;
    text-align: center;
  }

  .section-card {
    padding: 1.1rem;
    border: 1px solid var(--color-border);
    border-radius: 0.85rem;
  }

  .section-card h2 {
    margin: 0;
  }

  .section-header {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    flex-wrap: wrap;
    gap: 0.6rem;
    margin-bottom: 0.75rem;
  }

  .section-header a {
    font-weight: 600;
  }

  .reading {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1rem;
    margin-top: 0.75rem;
  }

  /* GitHub stats */
  .github-section h2 {
    margin-bottom: 0.85rem;
  }

  .github-loading,
  .github-error {
    margin: 0.5rem 0 0;
    font-size: 0.95rem;
    opacity: 0.7;
  }

  .github-stats {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 0 1rem;
    margin: 0;
  }

  .github-stats dt {
    grid-row: 1;
    font-size: 0.78rem;
    font-weight: 600;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    opacity: 0.65;
    padding-bottom: 0.2rem;
    border-bottom: 2px solid var(--color-accent, oklch(50% 30% 250));
  }

  .github-stats dd {
    grid-row: 2;
    margin: 0;
    font-size: 2rem;
    font-weight: 700;
    color: var(--color-accent, oklch(50% 30% 250));
    padding-top: 0.25rem;
  }

  @media (max-width: 760px) {
    .intro {
      grid-template-columns: 1fr;
      padding: 1rem;
    }

    .intro-image {
      max-width: 240px;
    }

    .section-card {
      padding: 1rem;
    }

    .github-stats {
      grid-template-columns: repeat(2, 1fr);
    }
  }
</style>
