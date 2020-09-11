<script>
  import { onMount } from 'svelte';
  import { token } from './stores.js';
  import List from './components/List.svelte';
  import * as process from 'process';

  const data = {
    grant_type: 'client_credentials',
    client_id: process.env.CLIENT_ID,
    client_secret: process.env.CLIENT_SECRET,
  }

  let result = '';
  let login = '';
  let logins = [];
  if (localStorage.getItem('logins')) {
    logins = localStorage.getItem('logins').split(' ');
  }

  onMount(async () => {
    const res = await fetch(`https://cors-anywhere.herokuapp.com/https://api.intra.42.fr/oauth/token`, {
      method: 'POST',
      headers: {
        'Access-Control-Request-Method': 'POST',
        'Origin': 'https://ft-stalker.vercel.app',
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });
    result = await res.json();
    token.set(result.access_token);

    if (localStorage.getItem('logins')) {
      logins = localStorage.getItem('logins').split(' ');
    }
  });

  async function handleEnter(event) {
    if (event.keyCode === 13 && $token != 0) {
      const res = await fetch(`https://cors-anywhere.herokuapp.com/https://api.intra.42.fr/v2/users/${login}?access_token=${$token}`);

      if (res.status == 200) {
        logins[logins.length] = login;
        localStorage.setItem('logins', logins.join(' '));
        login = '';
      }
    }
  }
</script>

<main>
  {#if result == {}}
    <p>loading</p>
  {:else}
    <h1>FT_STALKER</h1>
    <input bind:value={login} on:keydown={handleEnter} placeholder='login'>
    <List {logins} />
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  input {
    width: 60%;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>