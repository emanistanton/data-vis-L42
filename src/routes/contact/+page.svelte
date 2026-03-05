<script>
  function handleSubmit(event) {
    event.preventDefault();
    let form = event.target;
    let data = new FormData(form);

    let url = form.action + "?";
    for (let [name, value] of data) {
      url += (name + "=" + encodeURIComponent(value) + "&");
    }

    location.href = url;
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

  <button>Send</button>
</form>
