<script>
  export let results;

  $: messages = results
    .filter(([_, isValid]) => !isValid)
    .map(([pointer]) => pointer);
</script>

Invalid
<ul>
  {#each messages as message}
    <li>{message}</li>
  {/each}
</ul>
