<script>
  import Hyperjump from "@hyperjump/browser";
  import Validation from "@hyperjump/validation";


  let validation = "";
  const validationUrl = "https://mock.hyperjump.io/validation";

  $: validate = (async function () {
    if (validation !== "") {
      fetchStub.set(validationUrl, validation, "application/reference+json");

      const doc = Hyperjump.get(validationUrl, Hyperjump.nil);
      return Validation.validate(doc);
    }
  }());

  let subject = "";
  const subjectUrl = "https://mock.hyperjump.io/subject";

  $: result = (async function () {
    if (subject !== "") {
      fetchStub.set(subjectUrl, subject, "application/reference+json");

      const doc = Hyperjump.get(subjectUrl, Hyperjump.nil);

      let v;
      try {
        v = await validate;
      } catch (e) {}

      if (v) {
        return v(doc);
      }
    }
  }());
</script>

<svelte:head>
	<title>Hyperjump Validation</title>
</svelte:head>

<main>
  <h1>Hyperjump Validation</h1>
  <div class="foo">
    <div class="bar">
      <h2>Validation</h2>
      <textarea name="validation" bind:value={validation}></textarea>
      <pre class="results">
        {#await validate then v}
          {#if v}
            Valid
          {/if}
        {:catch error}
          {Array.isArray(error) ? JSON.stringify(error, null, "  ") : error}
        {/await}
      </pre>
    </div>
    <div class="bar">
      <h2>Subject</h2>
      <textarea name="subject" bind:value={subject}></textarea>
      <pre class="results">
        {#await result then r}
          {#if r}
            {Validation.isValid(r) ? "Valid" : JSON.stringify(r, null, "  ")}
          {/if}
        {:catch error}
          {error}
        {/await}
      </pre>
    </div>
  </div>
</main>

<style>
  .foo {
    display: flex;
    height: 100%;
    width: 100%;
  }

  .bar {
    display: flex;
    width: 100%;
    flex-direction: column;
    padding: 1em;
  }

  .results {
    border: thin solid black;
    padding: .5em;
    min-height: 100px;
    overflow: scroll;
  }

  h1 {
    margin: 1em auto;
  }

  textarea {
    display: block;
    height: 100%;
    border: thin solid black;
    min-height: 300px;
    font-size: 16px;
    padding: .5em;
    margin-bottom: 1em;
  }
</style>
