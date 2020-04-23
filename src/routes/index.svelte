<script>
  import Hyperjump from "@hyperjump/browser";
  import Validation from "@hyperjump/validation";
  import Editor from "../components/Editor.svelte";
  import EditorTabs from "../components/EditorTabs.svelte";
  import Results from "../components/Results.svelte";
  import Footer from "../components/Footer.svelte";


  const DEBOUNCE_DELAY = 750;
  const defaultMeta = "https://validation.hyperjump.io/common";
  const validationUrl = "https://validation.hyperjump.io/validation";
  const instanceUrl = "https://validation.hyperjump.io/instance";

  const theme = "solarized-dark";

  const newValidation = (function () {
    let sequence = 0;

    return (label = undefined, url = undefined, persistent = false) => {
      sequence++;
      const id = url || `${validationUrl}${sequence}`;
      return { id: id, label: label || `validation${sequence}`, text: `{
  "$meta": { "$href": "${defaultMeta}" }
}`, persistent: persistent };
    }
  }());

  const newInstance = (function () {
    let sequence = 0;

    return () => {
      sequence++;
      const id = `${instanceUrl}${sequence}`;
      return { id: id, label: `instance${sequence}`, text: "" };
    };
  }());

  let validations = [newValidation("validation", validationUrl, true)];
  let instances = [newInstance()];
  let selectedInstance = 0;

  const debounce = function (fn, delay) {
    let timer;
    return ({ detail }) => {
      clearTimeout(timer);
      timer = setTimeout(() => fn(detail), delay);
    };
  };
  const updateValidations = debounce((detail) => validations = detail, DEBOUNCE_DELAY);
  const updateInstances = debounce((detail) => instances = detail, DEBOUNCE_DELAY);

  $: validate = (async function () {
    if (typeof fetchStub !== "undefined" && validations[0].text) {
      validations.forEach((validation, ndx) => {
        fetchStub.set(validation.id, validation.text, "application/validation+json");
      });

      const doc = Hyperjump.fetch(validationUrl);
      return Validation.validate(doc);
    }
  }());

  $: validationResults = (async function () {
    if (typeof fetchStub !== "undefined" && instances[selectedInstance].text !== "") {
      fetchStub.set(instanceUrl, instances[selectedInstance].text, "application/reference+json");

      const doc = Hyperjump.fetch(instanceUrl);

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
  <title>Hyperjump Validation</title>
</svelte:head>

<main>
  <h1>Hyperjump Validation</h1>

  <div class="editor-section">
    <EditorTabs tabs={validations} newTab={newValidation} active={0} on:input={updateValidations} />
  </div>
  <div class="editor-section">
    <EditorTabs tabs={instances} bind:selected={selectedInstance} bind:active={selectedInstance} newTab={newInstance} on:input={updateInstances} />
  </div>

  <div class="results {theme}">
    <Results results={validate} />
  </div>
  <div class="results {theme}">
    {#await validate then _}
      <Results results={validationResults} />
    {/await}
  </div>

  <Footer />
</main>

<style>
  h1 {
    margin: auto;
    grid-column-end: span 2;
  }

  main {
    display: grid;
    grid-auto-flow: row;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto 1fr 200px auto;
    grid-gap: .5em;
    padding: .5em;
    height: 100%;

    --editor-padding: .25em;
    --editor-border: thin solid;
  }

  .editor-section {
    display: flex;
    flex-direction: column;
    overflow-y: hidden;
    min-height: 200px;
  }

  .results {
    border: thin solid;
    overflow: scroll;
    padding: .25em;
  }
</style>
