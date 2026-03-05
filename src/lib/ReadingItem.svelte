<script>
  export let data = {};
</script>

<article class="reading-item">
  <img src={data.image} alt={`Cover image for ${data.title}`} loading="lazy" />
  <div class="reading-copy">
    <h3>{data.title}</h3>
    <p class="reading-author">by {data.author}</p>
    {#if data.description}
      <p class="reading-description">{data.description}</p>
    {/if}
    {#if data.link}
      <p><a href={data.link} target="_blank" rel="noreferrer">Read more</a></p>
    {/if}
  </div>
</article>

<style>
  .reading-item {
    display: flex;
    gap: 1rem;
    align-items: flex-start;
    padding: 0.75rem;
    border: 1px solid var(--color-border);
    border-radius: 0.75rem;
  }

  img {
    width: 88px;
    aspect-ratio: 2 / 3;
    object-fit: cover;
    flex-shrink: 0;
    border-radius: 0.4rem;
    border: 1px solid var(--color-border);
  }

  h3 {
    margin: 0;
    font-size: 1rem;
  }

  .reading-author {
    margin: 0.15rem 0 0;
    font-weight: 600;
  }

  .reading-description {
    margin: 0.4rem 0;
  }

  .reading-copy :global(p:last-child) {
    margin-bottom: 0;
  }
</style>
