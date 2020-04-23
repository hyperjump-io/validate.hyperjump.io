<script>
  import GitHubLink from "./GitHubLink.svelte";
  import NpmLink from "./NpmLink.svelte";
</script>

<footer>
  <ul class="links">
    <li>
      <GitHubLink href="https://github.com/hyperjump-io/validate.hyperjump.io">
        hyperjump-io/validate.hyperjump.io
      </GitHubLink>
    </li>
    <li>
      <GitHubLink href="https://github.com/hyperjump-io/validation">
        hyperjump-io/validation
      </GitHubLink>
    </li>
    <li>
      <GitHubLink href="https://github.com/hyperjump-io/browser">
        hyperjump-io/browser
      </GitHubLink>
    </li>
    <li>
      <NpmLink href="https://www.npmjs.com/package/@hyperjump/validation">
        @hyperjump/validation
      </NpmLink>
    </li>
    <li>
      <NpmLink href="https://www.npmjs.com/package/@hyperjump/browser">
        @hyperjump/browser
      </NpmLink>
    </li>
  </ul>
</footer>

<style>
  footer {
    margin: auto;
    grid-column-end: span 2;
  }

  ul {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    list-style: none;
    margin: 0;
    padding: 0;
  }

  li:not(:first-child) {
    margin-left: 1em;
  }
</style>
