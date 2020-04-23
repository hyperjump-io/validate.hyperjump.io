<script>
  export let results;
</script>

{#await results}
  Validating ...
{:then results}
  {results ? "Valid" : ""}
{:catch error}
  {#if Array.isArray(error)}
    Invalid
    <ul>
      {#each error as result}
        {#if !result.isValid}
          <li><code>{result.valueUrl}</code> fails constraint <code>{result.validationUrl}</code></li>
        {/if}
      {/each}
    </ul>
  {:else}
    {error}
  {/if}
{/await}

<style>
  code {
    background-color: var(--line-focus-background-color);
    padding: .25em;
    border-radius: .25em;
  }

  li {
    margin: .5em;
    line-height: 1.5em;
  }
</style>
