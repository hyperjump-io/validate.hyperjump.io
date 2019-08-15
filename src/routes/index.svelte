<script>
  import Hyperjump from "@hyperjump/browser";
  import Validation from "@hyperjump/validation";
  import Results from "../components/Results.svelte";


  let validation = `{
  "$meta": { "$href": "https://validation.hyperjump.io/common" },
  "type": "object"
}`;
  const validationUrl = "https://validate.hyperjump.io/validation";

  $: validate = (async function () {
    if (typeof fetchStub !== "undefined" && validation !== "") {
      fetchStub.set(validationUrl, validation, "application/validation+json");

      const doc = Hyperjump.fetch(validationUrl);
      return Validation.validate(doc);
    }
  }());

  let subject = "{}";
  const subjectUrl = "https://mock.hyperjump.io/subject";

  $: results = (async function () {
    if (typeof fetchStub !== "undefined" && subject !== "") {
      fetchStub.set(subjectUrl, subject, "application/reference+json");

      const doc = Hyperjump.fetch(subjectUrl);

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
  <title>Hyperjump Validate</title>
</svelte:head>

<h1>Hyperjump Validate</h1>

<main>
  <section>
    <h2>Validation</h2>
    <textarea class="editor" bind:value={validation}></textarea>
    <div class="results">
      {#await validate then v}
        {#if v}
          Valid
        {/if}
      {:catch error}
        {#if Array.isArray(error)}
          <Results results={error} />
        {:else}
          {error}
        {/if}
      {/await}
    </div>
  </section>
  <section>
    <h2>Subject</h2>
    <textarea class="editor" bind:value={subject}></textarea>
    <div class="results">
      {#await results then r}
        {#if r}
          {#if Validation.isValid(r)}
            Valid
          {:else}
            <Results results={r} />
          {/if}
        {/if}
      {:catch error}
        {error}
      {/await}
    </div>
  </section>
</main>

<style>
  h1 {
    margin: 1em auto;
  }

  main {
    display: flex;
    height: 100%;
  }

  section {
    display: flex;
    flex: 1;
    flex-direction: column;
    padding: 1em;
  }

  .editor {
    height: 100%;
    min-height: 300px;
    padding: .5em;
    margin-bottom: 1em;
    font-size: 16px;
    border: thin solid black;
  }

  .results {
    padding: .5em;
    margin: 0;
    min-height: 100px;
    border: thin solid black;
  }
</style>
