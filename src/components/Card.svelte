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

    while (res.status == 429) {
      await sleep(1000);
      res = await fetch(`https://cors-anywhere.herokuapp.com/https://api.intra.42.fr/v2/users/${login}?access_token=${$token}`);
    }

    result = await res.json();
    console.log(result);
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
      Piscine: {result.pool_month} {result.pool_year}<br />
      Level: {level}<br />
      Points de corrections: {result.correction_point}<br />
      Location: {result.location}<br />
    </p>
    <h3>Notes</h3>
    <div>
      {#each notes as note}
        <div class='note'>
          <span class='symboles'>
            {#if note['validated?'] == true}
              ✔
            {:else if note['validated?'] == false}
              ✘
            {/if}
            {#if note.status == 'in_progress'}
              ☐
            {/if}
          </span>
          <span class='name'>{note.project.name}:</span>
          <span class='final_mark'>
            {#if note.final_mark}
              {note.final_mark}
            {/if}
          </span><br />
        </div>
      {:else}
        loading
      {/each}
    </div>
  {/if}

  <span class='supress' on:click={removeUser}>supprimer</span>
</main>

<style>
  main {
    position: relative;
    width: 300px;
    max-height: 1200px;
    padding-bottom: 50px;
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

  div.note {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
  }

  span.symboles {
    width: 50px;
    align-self: flex-start;
  }

  span.name {
    width: 200px;
  }

  span.final_mark {
    width: 50px;
  }
</style>