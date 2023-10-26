<script lang="ts">
  import { createEventDispatcher, onMount, tick } from "svelte";
  import { fade, slide } from "svelte/transition";
  import Button, { Label, Icon } from "@smui/button";

  enum SOURCES {
    imdb = "Internet Movie Database",
    rt = "Rotten Tomatoes",
    mc = "Metacritic",
  }
  export let movieTitle: string;
  export let showRatings = false;
  export let borderColor = "black";
  export let movieIndex = -1;

  let movieDetails: any;
  let posterImage: any;
  const dispatch = createEventDispatcher();

  $: fetchMovieDetails(movieTitle);

  async function fetchMovieDetails(title: string) {
    const apiKey = "ee32e460";
    const response = await fetch(
      `https://www.omdbapi.com/?t=${title}&apikey=${apiKey}`
    );
    movieDetails = await response.json();
    dispatch("imdbScore", parseFloat(movieDetails.Ratings[0].Value));
  }

  function handleHigherClick() {
    dispatch("higherClicked", { movieIndex });
    showRatings = true;
  }
  function handleLowerClick() {
    dispatch("lowerClicked", { movieIndex });
    showRatings = true;
  }
  function getSourceClass(source: string) {
    switch (source) {
      case SOURCES.imdb:
        return "imdb";
      case SOURCES.rt:
        return "rotten";
      case SOURCES.mc:
        return "metacritic";
    }
  }
</script>

{#if movieDetails && !movieDetails.Error}
  <div
    class={`Card ${
      borderColor === "green"
        ? "BorderGreen"
        : borderColor === "red"
        ? "BorderRed"
        : ""
    }`}
    in:fade={{ delay: 200, duration: 300 }}
  >
    <img
      src={movieDetails.Poster}
      in:fade={{ duration: 500 }}
      class="Poster"
      alt="poster"
    />
    <div class="Title">
      <h2>
        {movieDetails.Title}
      </h2>
      <h3>{movieDetails.Year}</h3>
    </div>
    <div class="Description">
      <h4>
        {movieDetails.Plot}
      </h4>
    </div>

    {#if showRatings === false}
      <div class="HLButtons">
        <Button
          on:click={handleHigherClick}
          variant="unelevated"
          class="IconButton"
        >
          <Icon class="material-icons">arrow_upward</Icon>
          <Label>Higher</Label>
        </Button>
        <Button
          on:click={handleLowerClick}
          variant="unelevated"
          class="IconButton"
        >
          <Icon class="material-icons">arrow_downward</Icon>
          <Label>Lower</Label>
        </Button>
      </div>
    {:else}
      <div class="Ratings" in:fade={{ duration: 200 }}>
        {#each movieDetails.Ratings as rating}
          <div class={getSourceClass(rating.Source)}>
            <img
              class="RatingImage"
              src={`${getSourceClass(rating.Source)}.png`}
              alt="source"
            />
            <h4>{rating.Value}</h4>
          </div>
        {/each}
      </div>
    {/if}
  </div>
{/if}

<style lang="scss">
  h2 {
    font-size: clamp(1rem, 0.8846rem + 0.5128vw, 1.5rem);
  }
  h4 {
    font-size: clamp(0.625rem, 0.5385rem + 0.3846vw, 1rem);
  }
  .Card {
    overflow: hidden;

    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 0.5fr 1fr 1fr;
    grid-template-areas: "poster title" "poster description" "poster ratings";
    border-radius: 10px;
    max-width: 700px;
    height: 500px;
    background-color: rgba(60, 60, 60, 0.9);

    .Poster {
      width: 100%;
      height: 100%;
      object-fit: cover;
      grid-area: poster;
      border-top-left-radius: 10px;
      border-bottom-left-radius: 10px;
    }

    .Title {
      grid-area: title;
      text-align: center;
    }
    .Description {
      grid-area: description;
      text-align: center;
      align-items: center;
      overflow: hidden;
      padding: 0 16px;
    }

    .HLButtons {
      display: flex;
      flex-direction: column;
      gap: 50px;
      width: 80%;
      place-self: center;
      justify-content: center;
    }
    .Ratings {
      grid-area: ratings;
      display: grid;
      place-content: center;
      grid-template-columns: 1fr 1fr;
      grid-template-rows: 1fr;
      grid-template-areas: "imdb imdb" "rotten mt";
      flex-direction: column;
      padding: 16px;

      font-weight: bold;
      .RatingRow {
        display: inline-flex;
        gap: 16px;
      }
      .imdb .RatingImage {
        width: 100px;
      }

      .rotten .RatingImage,
      .metacritic .RatingImage {
        width: 40px;
      }
      .imdb {
        grid-area: imdb;

        justify-content: center;
      }
      .rotten {
        grid-area: rotten;
      }
      .metacritic {
        grid-area: mt;
      }
      .imdb,
      .rotten,
      .metacritic {
        display: flex;
        flex-direction: row;

        gap: 16px;
        align-items: center;
        margin: 0 8px;
      }
    }
  }
  .BorderGreen {
    animation: pulse-animation-green 1s ease;
  }
  .BorderRed {
    animation: pulse-animation-red 1s ease;
  }
  @keyframes pulse-animation-red {
    0% {
      box-shadow: 0 0 0 0px rgba(255, 0, 0, 0.8);
    }
    100% {
      box-shadow: 0 0 0 30px rgba(255, 0, 0, 0);
    }
  }

  @keyframes pulse-animation-green {
    0% {
      box-shadow: 0 0 0 0px rgba(0, 255, 0, 0.8);
    }
    100% {
      box-shadow: 0 0 0 30px rgba(0, 255, 0, 0);
    }
  }

  .pulse-red {
    animation: pulse-animation-red 2s infinite;
  }

  .pulse-green {
    animation: pulse-animation-green 2s infinite;
  }

  @media screen and (max-width: 1250px) {
    .Card {
      height: 340px;
      width: 600px;
    }
    .Description {
      display: none;
    }
  }
  @media screen and (max-width: 675px) {
    .Card {
      width: 400px;
      grid-template-rows: 1fr 1fr;
    }
  }
  @media screen and (max-width: 475px) {
    .Card {
      width: 300px;
      height: 300px;

      .Poster {
        width: 150px;
        height: 100%;
        object-fit: cover;
      }
      .Ratings {
        .imdb,
        .rotten,
        .metacritic {
          justify-content: flex-start;
          gap: 4px;
          margin: 0;
        }
        .imdb .RatingImage {
          width: 80px;
        }
        .rotten .RatingImage,
        .metacritic .RatingImage {
          width: 20px;
        }
      }
    }
  }
</style>
