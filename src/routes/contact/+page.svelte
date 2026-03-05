<script>
  function handleSubmit(event) {
    event.preventDefault();
    const form = event.currentTarget;
    const data = new FormData(form);
    const action = form.getAttribute("action") ?? "";

    let url = action + "?";
    const params = [];
    for (let [name, value] of data) {
      params.push(name + "=" + encodeURIComponent(String(value)));
    }
    url += params.join("&");

    window.location.href = url;
  }
</script>

<svelte:head>
  <title>Contact me</title>
</svelte:head>

<h1>Contact Me</h1>

<form action="mailto:emani04@mit.edu" method="GET" on:submit={handleSubmit}>
  <label>
    Subject:
    <input name="subject" placeholder="Hello" required />
  </label>
  <label>
    Message:
    <textarea name="body" placeholder="Sup?" required></textarea>
  </label>

  <button type="submit">Send</button>
</form>
