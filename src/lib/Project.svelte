<script>
  import { base } from "$app/paths";
  export let data = {};
</script>

<article>
  {#if data.year}
    <span class="year">{data.year}</span>
  {/if}
  <h2>
    {#if data.url}
      <a href={data.url} target="_blank" rel="noopener">{data.title}</a>
    {:else}
      {data.title}
    {/if}
  </h2>
  <img src={data.image?.startsWith("http") ? data.image : `${base}${data.image}`} alt="" />
  <p>{data.description}</p>
</article>

<style>
  article {
    display: grid;
    grid-template-rows: subgrid;
    grid-row: span 4;
  }

  h2 {
    margin: 0;
  }

  img {
    max-width: 100%;
  }

  .year {
    font-size: 0.8rem;
    font-weight: 600;
    letter-spacing: 0.05em;
    text-transform: uppercase;
    color: var(--color-accent, oklch(50% 30% 250));
    opacity: 0.75;
  }
</style>
