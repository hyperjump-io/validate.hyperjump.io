<script>
  export let results;
</script>

{#await results}
  Validating ...
{:then _}
  Valid
{:catch error}
  {#if Array.isArray(error)}
    Invalid
    <ul>
      {#each error as message}
        {#if !message.isValid}
          <li>{message.validationUrl}</li>
        {/if}
      {/each}
    </ul>
  {:else}
    {error}
  {/if}
{/await}
