<script lang="ts">
  import Button from "@smui/button";
  import Card from "./Card.svelte";

  import { fade } from "svelte/transition";
  import { onMount } from "svelte";
  import SoundOn from "./SoundOn.svelte";
  import SoundOff from "./SoundOff.svelte";

  let score = 0;
  let showCardRatings = [true, false];
  let imdbScores = [null, null];
  let showTryAgainButton = false;
  let borderColor = ["orange", "orange"];
  let currentMovieIndex = 1;
  let progressBar: HTMLDivElement | null = null;
  let showProgressBar = false;
  let hasAudio = true;
  let highScore = 0;
  let correctSound: HTMLAudioElement | null = null;
  let wrongSound: HTMLAudioElement | null = null;
  let hasMovieLoaded = false;
  export let mode = "";
  onMount(() => {
    highScore = Number(localStorage.getItem(mode)) ?? 0;
    correctSound = new Audio("/correct.mp3");
    wrongSound = new Audio("/wrong.mp3");
  });

  export let slicedContent = [""];
  let initialContent = [...slicedContent];
  let currentMovies = [getRandomMovie(), getRandomMovie()];
  function getRandomMovie() {
    const randomIndex = Math.floor(Math.random() * slicedContent.length);
    return slicedContent.splice(randomIndex, 1)[0];
  }

  function handleGameOver() {
    wrongSound?.play();
    borderColor[currentMovieIndex] = "red";
    showTryAgainButton = true;
  }
  function loadNextMovie(movieIndex: number) {
    showProgressBar = false;
    if (imdbScores[0] && imdbScores[1] && slicedContent) {
      if (movieIndex === 0) {
        showCardRatings[0] = true;
        showCardRatings[1] = false;
        currentMovieIndex = 1;
      } else {
        showCardRatings[0] = false;
        showCardRatings[1] = true;
        currentMovieIndex = 0;
      }
      currentMovies[currentMovieIndex] = getRandomMovie();
      if (progressBar) {
        progressBar.style.width = "0px";
        progressBar.style.transition = "none";
      }
    }
  }
  function updateCards(movieIndex: number) {
    score += 100;
    correctSound?.play();
    if (highScore < score) {
      highScore = score;
      localStorage.setItem(mode, String(highScore));
    }
    borderColor[movieIndex] = "green";
    showProgressBar = true;
    requestAnimationFrame(() => {
      if (progressBar) {
        progressBar.style.transition = "width 2.5s linear";
        progressBar.style.width = "100%";
      }
    });
    setTimeout(() => {
      loadNextMovie(movieIndex);
      borderColor[movieIndex] = "orange";
    }, 2500);
  }

  export function checkHigher({ movieIndex }: { movieIndex: number }) {
    if (imdbScores[0] && imdbScores[1]) {
      switch (movieIndex) {
        case 0: {
          if (imdbScores[0] >= imdbScores[1]) {
            updateCards(movieIndex);
          } else handleGameOver();
          break;
        }
        case 1: {
          if (imdbScores[0] <= imdbScores[1]) {
            updateCards(movieIndex);
          } else handleGameOver();
          break;
        }
      }
    }
  }
  export function checkLower({ movieIndex }: { movieIndex: number }) {
    if (imdbScores[0] && imdbScores[1]) {
      switch (movieIndex) {
        case 0: {
          if (imdbScores[0] <= imdbScores[1]) {
            updateCards(movieIndex);
          } else handleGameOver();
          break;
        }
        case 1: {
          if (imdbScores[0] >= imdbScores[1]) {
            updateCards(movieIndex);
          } else handleGameOver();
          break;
        }
      }
    }
  }
  const handleMute = () => {
    if (correctSound && wrongSound) {
      hasAudio = false;
      correctSound.muted = true;
      wrongSound.muted = true;
    }
  };

  const handleUnmute = () => {
    if (correctSound && wrongSound) {
      hasAudio = true;
      correctSound.muted = false;
      wrongSound.muted = false;
    }
  };

  const handleTryAgain = () => {
    score = 0;

    showCardRatings = [true, false];
    imdbScores = [null, null];
    showTryAgainButton = false;
    slicedContent = [...initialContent];
    currentMovies = [getRandomMovie(), getRandomMovie()];
    currentMovieIndex = 1;
    borderColor[currentMovieIndex] = "orange";
  };
</script>

<div class="Cards">
  {#if currentMovies.length === 2}
    <Card
      movieIndex={0}
      movieTitle={currentMovies[0]}
      showRatings={showCardRatings[0]}
      borderColor={borderColor[0]}
      on:imdbScore={(e) => (imdbScores[0] = e.detail)}
      on:higherClicked={(e) => checkHigher(e.detail)}
      on:lowerClicked={(e) => checkLower(e.detail)}
    />
    {#if hasMovieLoaded}
      <div class="ScoreData" in:fade={{ duration: 500 }}>
        <h4>Score: {score}</h4>

        {#if showTryAgainButton}
          <div in:fade={{ duration: 500 }} class="TryAgainButton">
            <Button on:click={handleTryAgain} variant="raised">Try again</Button
            >
          </div>
        {:else}
          <div class="progress-bar">
            <div class="progress" bind:this={progressBar} />
          </div>
        {/if}
        {#if hasAudio}
          <div
            class="sound"
            on:click={handleMute}
            on:keydown={(e) =>
              ["Enter", "Space"].includes(e.code) && handleMute()}
            role="button"
            tabindex={0}
          >
            <SoundOn />
          </div>
        {:else}
          <div
            class="sound"
            on:click={handleUnmute}
            on:keydown={(e) =>
              ["Enter", "Space"].includes(e.code) && handleUnmute()}
            role="button"
            tabindex={0}
          >
            <SoundOff />
          </div>
        {/if}
        <h4>High score: {highScore}</h4>
      </div>
    {/if}
    <Card
      movieIndex={1}
      movieTitle={currentMovies[1]}
      borderColor={borderColor[1]}
      showRatings={showCardRatings[1]}
      on:imdbScore={(e) => (imdbScores[1] = e.detail)}
      on:higherClicked={(e) => checkHigher(e.detail)}
      on:lowerClicked={(e) => checkLower(e.detail)}
      on:movieLoaded={(e) => (hasMovieLoaded = e.detail)}
    />
  {/if}
</div>

<style lang="scss">
  .ScoreData {
    padding: 8px;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    gap: 32px;
    width: 80%;
    background-color: var(--card-background);
    text-align: center;
    align-items: center;
    h4 {
      margin-block-start: 0;
      margin-block-end: 0;
      font-size: clamp(0.625rem, 0.5385rem + 0.3846vw, 1rem);
    }
    .sound {
      display: flex;
      cursor: pointer;
    }
  }
  .Cards {
    display: grid;
    grid-template-columns: 1fr 0.5fr 1fr;
    grid-template-rows: 1fr;
    gap: 1vw;
    padding: 0 5vw;
    place-items: center;
    height: 100dvh;
  }

  .progress-bar {
    width: 100%;
    margin: 8px 0;
    height: 20px;

    background-color: rgba(224, 224, 224, 0.7);
    border-radius: 10px;
    overflow: hidden;
  }
  .try-again-container {
    height: 36px;
  }
  .progress {
    height: 100%;
    width: 0;
    background-color: orange;
  }

  @media screen and (max-width: 1250px) {
    .Cards {
      grid-template-columns: 1fr;
      grid-template-rows: 1fr 0.1fr 1fr;
    }
    .ScoreData {
      flex-direction: row;
      width: 100%;
      justify-content: center;
    }
    .progress-bar {
      max-width: 200px;
    }
  }
  @media screen and (max-width: 475px) {
    .progress-bar {
      max-width: 100px;
    }
    .ScoreData {
      gap: 16px;
      .TryAgainButton :global(.mdc-button) {
        width: 100px;
        font-size: 11px;
      }
    }
  }
</style>
