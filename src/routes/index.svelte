<script>
  import Hyperjump from "@hyperjump/browser";
  import Validation from "@hyperjump/validation";
  import Editor from "../components/Editor.svelte";
  import Results from "../components/Results.svelte";


  let theme = "solarized-dark";
  let validation = `{
  "$meta": { "$href": "https://validation.hyperjump.io/common" },
  "type": "object"
}`;

  let subject = `{}`;

  $: validate = (async function () {
    const validationUrl = "https://validate.hyperjump.io/validation";
    if (typeof fetchStub !== "undefined" && validation !== "") {
      fetchStub.set(validationUrl, validation, "application/validation+json");

      const doc = Hyperjump.fetch(validationUrl);
      return Validation.validate(doc);
    }
  }());

  $: validationResults = (async function () {
    const subjectUrl = "https://validate.hyperjump.io/subject";
    if (typeof fetchStub !== "undefined" && subject !== "") {
      fetchStub.set(subjectUrl, subject, "application/reference+json");

      const doc = Hyperjump.fetch(subjectUrl);

      let v;
      try {
        v = await validate;
      } catch (e) {}

      if (v) {
        const results = await v(doc);
        if (Validation.isValid(results)) {
          return results;
        } else {
          throw results;
        }
      }
    }
  }());
</script>

<svelte:head>
  <title>Hyperjump Validate</title>
</svelte:head>

<h1>Hyperjump Validate</h1>

<main>
  <h2>Validation</h2>
  <Editor bind:value={validation} />
  <div class="results {theme}">
    <Results results={validate} />
  </div>

  <h2>Subject</h2>
  <Editor bind:value={subject} />
  <div class="results {theme}">
    {#await validate then _}
      <Results results={validationResults} />
    {/await}
  </div>
</main>

<style>
  h1 {
    margin: auto;
  }

  h2 {
    margin: 0;
  }

  main {
    display: grid;
    grid-auto-flow: column;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1.25em 1fr 200px;;
    grid-gap: .5em;
    margin: .5em;
    height: 90%;
  }

  .results {
    border: thin solid;
    overflow: scroll;
    padding: .25em;
  }
</style>
