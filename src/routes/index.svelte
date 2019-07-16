<script>
  import Hyperjump from "@hyperjump/browser";
  import Validation from "@hyperjump/validation";


  let validation = `{
  "$meta": { "$href": "https://validation.hyperjump.io/common" },
  "type": "object"
}`;
  const validationUrl = "https://mock.hyperjump.io/validation";

  $: validate = (async function () {
    if (typeof fetchStub !== "undefined" && validation !== "") {
      fetchStub.set(validationUrl, validation, "application/validation+json");

      const doc = Hyperjump.get(validationUrl, Hyperjump.nil);
      return Validation.validate(doc);
    }
  }());

  let subject = "{}";
  const subjectUrl = "https://mock.hyperjump.io/subject";

  $: result = (async function () {
    if (typeof fetchStub !== "undefined" && subject !== "") {
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

<h1>Hyperjump Validation</h1>

<main>
    <section>
      <h2>Validation</h2>
      <textarea aria-label="Validation" bind:value={validation}></textarea>
      <pre>
        {#await validate then v}
          {#if v}
            Valid
          {/if}
        {:catch error}
          {Array.isArray(error) ? JSON.stringify(error, null, "  ") : error}
        {/await}
      </pre>
    </section>
    <section>
      <h2>Subject</h2>
      <textarea aria-label="Subject" bind:value={subject}></textarea>
      <pre>
        {#await result then r}
          {#if r}
            {Validation.isValid(r) ? "Valid" : JSON.stringify(r, null, "  ")}
          {/if}
        {:catch error}
          {error}
        {/await}
      </pre>
    </section>
</main>

<style>
  main {
    display: flex;
    height: 100%;
  }

  section {
    display: flex;
    width: 100%;
    flex-direction: column;
    padding: 1em;
  }

  pre {
    border: thin solid black;
    padding: .5em;
    margin: 0;
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
