<script>
  import Card from './Card.svelte';
  import { token } from '../stores.js';

  export let logins;
</script>

<main>
  {#each logins as login}
    {#if $token != 0}
      <Card {login} />
    {:else}
      <p>loading</p>
    {/if}
  {:else}
    <h1 class='alone'>NO PROFILE YET</h1>
  {/each}
</main>

<style>
  main {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
    column-gap: 10px;
    row-gap: 20px;
  }

  .alone {
    grid-column-start: 3;
  }
</style>