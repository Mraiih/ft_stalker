<script>
  import { onMount } from 'svelte';
  import { token } from '../stores.js';
  export let login;

  let result = {};
  let notes = [];
  let level = 0;

  onMount(async () => {
    let res = '';
    res = await fetch(`https://cors-anywhere.herokuapp.com/https://api.intra.42.fr/v2/users/${login}?access_token=${$token}`);

    if (res.status == 429) {
      await sleep(1000);
      res = await fetch(`https://cors-anywhere.herokuapp.com/https://api.intra.42.fr/v2/users/${login}?access_token=${$token}`);
    }

    result = await res.json();
    parseInformation(result);
  });

  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  function parseInformation(json) {
    let projets = json.projects_users;

    notes = [
      projets.find(project => project.project.slug == "c-piscine-shell-00"),
      projets.find(project => project.project.slug == "c-piscine-shell-01"),
      projets.find(project => project.project.slug == "c-piscine-c-00"),
      projets.find(project => project.project.slug == "c-piscine-c-01"),
      projets.find(project => project.project.slug == "c-piscine-c-02"),
      projets.find(project => project.project.slug == "c-piscine-c-03"),
      projets.find(project => project.project.slug == "c-piscine-c-04"),
      projets.find(project => project.project.slug == "c-piscine-c-05"),
      projets.find(project => project.project.slug == "c-piscine-c-06"),
      projets.find(project => project.project.slug == "c-piscine-c-07"),
      projets.find(project => project.project.slug == "c-piscine-c-08"),
      projets.find(project => project.project.slug == "c-piscine-c-09"),
      projets.find(project => project.project.slug == "c-piscine-c-10"),
      projets.find(project => project.project.slug == "c-piscine-c-11"),
      projets.find(project => project.project.slug == "c-piscine-c-12"),
      projets.find(project => project.project.slug == "c-piscine-c-13"),
      projets.find(project => project.project.slug == "c-piscine-rush-00"),
      projets.find(project => project.project.slug == "c-piscine-rush-01"),
      projets.find(project => project.project.slug == "c-piscine-rush-02"),
      projets.find(project => project.project.slug == "c-piscine-exam-00"),
      projets.find(project => project.project.slug == "c-piscine-exam-01"),
      projets.find(project => project.project.slug == "c-piscine-exam-02"),
      projets.find(project => project.project.slug == "c-piscine-exam-03")
    ];

    notes = notes.filter(note => note != undefined);

    level = json.cursus_users.find(cursus => cursus.level != 0);
    level = level.level
  }

  function removeUser() {
    let logins = [];

    if (localStorage.getItem('logins')) {
      logins = localStorage.getItem('logins').split(' ');
    }

    logins = logins.filter(user => user != login);
    localStorage.setItem('logins', logins.join(' '));
    location.reload();
  }
</script>

<main>
  {#if result == {}}
    <p>loading</p>
  {:else}
    <img src={result.image_url} alt='avatar' />
    <h2>{result.displayname}</h2>
    <p>
      Level: {level}<br />
      Points de corrections: {result.correction_point}<br />
      Location: {result.location}<br />
    </p>
    <h3>Notes</h3>
    <p>
      {#each notes as note}
        {note.project.name} : {note.final_mark}<br />
      {:else}
        loading
      {/each}
    </p>
  {/if}

  <span class='supress' on:click={removeUser}>supprimer</span>
</main>

<style>
  main {
    position: relative;
    width: 400px;
    max-height: 1200px;
    padding-bottom: 20px;
    border: 1px solid white;
    border-radius: 5px;
  }

  span.supress {
    position: absolute;
    bottom: 5px;
    right: 5px;
  }

  img {
    max-height: 100%;
    max-width: 100%;
  }
</style>