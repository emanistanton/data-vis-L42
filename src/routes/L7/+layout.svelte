<script>
  import "../../style.css";
  import { base } from "$app/paths";
  import { page } from "$app/stores";
  import { browser } from "$app/environment";

  let colorScheme = (browser && globalThis.localStorage?.colorScheme) || "light dark";
  let root = globalThis.document?.documentElement;

  $: root?.style.setProperty("color-scheme", colorScheme);
  $: if (browser) globalThis.localStorage.colorScheme = colorScheme;

  let pages = [
    { url: "/L7", title: "Home" },
    { url: "/L7/contact", title: "Contact" },
    { url: "/L7/projects", title: "Projects" },
    { url: "/L7/resume", title: "Resume" },
    { url: "/L7/meta", title: "Meta" },
    { url: "https://github.com/emanistanton", title: "GitHub" },
  ];
</script>

<label class="color-scheme-switch">
  Theme:
  <select bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
</label>

<nav>
  <ul>
    {#each pages as p}
      <li>
        <a
          href={p.url.startsWith("http") ? p.url : base + p.url}
          class:current={p.url === "/L7"
            ? $page.url.pathname === base + "/L7" || $page.url.pathname === base + "/L7/"
            : $page.url.pathname.startsWith(base + p.url)}
          target={p.url.startsWith("http") ? "_blank" : null}
        >
          {p.title}
        </a>
      </li>
    {/each}
  </ul>
</nav>

<slot />

<style>
  :root {
    color-scheme: light dark;
  }

  nav {
    --border-color: oklch(50% 10% 200 / 40%);
    display: flex;
    margin-bottom: 1em;
    border-bottom: 2px solid var(--border-color);
  }

  nav a {
    flex: 1;
    text-decoration: none;
    color: inherit;
    text-align: center;
    padding: 0.5em;
  }

  nav a.current {
    border-bottom: 4px solid var(--border-color);
    font-weight: bold;
    padding-bottom: 0;
  }

  nav a:hover {
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
    border-bottom: var(--color-accent) solid 0.4em;
    padding-bottom: 0;
  }

  nav ul {
    display: contents;
  }

  nav li {
    display: contents;
  }

  .color-scheme-switch {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    gap: 4px;
    font-size: 80%;
    width: fit-content;
    margin-right: auto;
  }
</style>
