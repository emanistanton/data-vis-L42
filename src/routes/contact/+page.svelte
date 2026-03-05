<script>
  import { base } from "$app/paths";
</script>

<svelte:head>
  <title>Contact me</title>
</svelte:head>

<nav>
  <ul>
    <li><a href={`${base}/`}>Home</a></li>
    <li><a href={`${base}/contact`} class="current">Contact</a></li>
    <li><a href={`${base}/projects`}>Projects</a></li>
    <li><a href={`${base}/resume`}>Resume</a></li>
    <li><a href="https://github.com/emanistanton" target="_blank">GitHub</a></li>
  </ul>
</nav>

<h1>Contact Me</h1>

<form id="contact-form" action="mailto:emani04@mit.edu" method="get">
  <label>
    Email:
    <input name="email" type="email" placeholder="example@gmail.com" required />
  </label>
  <label>
    Subject:
    <input name="subject" placeholder="Hello" required />
  </label>
  <label>
    Message:
    <textarea name="body" placeholder="Sup?" required></textarea>
  </label>

  <button>Send</button>
</form>
