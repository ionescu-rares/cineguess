<script lang="ts">
  import Button from "@smui/button";
  import Card from "./Card.svelte";
  import { movies } from "./movies";

  let score = 0;
  let showCardRatings = [true, false];
  let imdbScores = [null, null];
  let showTryAgainButton = false;
  let borderColor = ["orange", "orange"];
  let currentMovieIndex = 1;
  let progressBar: HTMLDivElement | null = null;
  let showProgressBar = false;
  let highScore = 0;
  let slicedMovies = movies;
  let currentMovies = [
    getRandomMovie(slicedMovies),
    getRandomMovie(slicedMovies),
  ];
  function getRandomMovie(movies: string[]): string {
    const randomIndex = Math.floor(Math.random() * slicedMovies.length);
    return slicedMovies.splice(randomIndex, 1)[0];
  }

  function handleGameOver() {
    borderColor[currentMovieIndex] = "red";
    showTryAgainButton = true;
  }
  function loadNextMovie(movieIndex: number) {
    showProgressBar = false;
    if (imdbScores[0] && imdbScores[1] && slicedMovies) {
      if (movieIndex === 0) {
        showCardRatings[0] = true;
        showCardRatings[1] = false;
        currentMovieIndex = 1;
      } else {
        showCardRatings[0] = false;
        showCardRatings[1] = true;
        currentMovieIndex = 0;
      }
      currentMovies[currentMovieIndex] = getRandomMovie(slicedMovies);
      if (progressBar) {
        progressBar.style.width = "0px";
        progressBar.style.transition = "none";
      }
    }
  }
  function updateCards(movieIndex: number) {
    score += 100;
    if (highScore < score) {
      highScore = score;
    }
    borderColor[movieIndex] = "green";
    showProgressBar = true;
    requestAnimationFrame(() => {
      if (progressBar) {
        progressBar.style.transition = "width 2.5s linear"; // Apply the transition effect here
        progressBar.style.width = "100%";
      }
    });
    setTimeout(() => {
      loadNextMovie(movieIndex);
      borderColor[movieIndex] = "orange";
    }, 2500);
  }

  export function checkHigher({ movieIndex }: { movieIndex: number }) {
    console.log("MOVIEINDEX", movieIndex);
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

  const handleTryAgain = () => {
    score = 0;
    slicedMovies = movies;
    showCardRatings = [true, false];
    imdbScores = [null, null];
    showTryAgainButton = false;

    currentMovies = [
      getRandomMovie(slicedMovies),
      getRandomMovie(slicedMovies),
    ];

    borderColor[currentMovieIndex] = "orange";
  };
</script>

<div class="Page">
  <div class="Cards">
    {#if currentMovies[0]}
      <Card
        movieIndex={0}
        movieTitle={currentMovies[0]}
        showRatings={showCardRatings[0]}
        borderColor={borderColor[0]}
        on:imdbScore={(e) => (imdbScores[0] = e.detail)}
        on:higherClicked={(e) => checkHigher(e.detail)}
        on:lowerClicked={(e) => checkLower(e.detail)}
      />
    {/if}
    <div class="ScoreData">
      <h1>High score: {highScore}</h1>

      <div class="progress-bar">
        <div class="progress" bind:this={progressBar} />
      </div>
      <div class="try-again-container">
        {#if showTryAgainButton}
          <Button on:click={handleTryAgain} variant="raised">Try again</Button>
        {/if}
      </div>
      <h2>Score: {score}</h2>
    </div>
    {#if currentMovies[1]}
      <Card
        movieIndex={1}
        movieTitle={currentMovies[1]}
        borderColor={borderColor[1]}
        showRatings={showCardRatings[1]}
        on:imdbScore={(e) => (imdbScores[1] = e.detail)}
        on:higherClicked={(e) => checkHigher(e.detail)}
        on:lowerClicked={(e) => checkLower(e.detail)}
      />
    {/if}
  </div>
</div>

<style lang="scss">
  h1 {
    font-size: clamp(1rem, 0.7692rem + 1.0256vw, 2rem);
  }
  h2 {
    font-size: clamp(1rem, 0.8846rem + 0.5128vw, 1.5rem);
  }
  h4 {
    font-size: clamp(0.625rem, 0.5385rem + 0.3846vw, 1rem);
    margin-block-end: 0;
  }
  .ScoreData {
    display: flex;
    flex-direction: column;
    gap: 32px;
    text-align: center;
    align-items: center;
    h2 {
      margin-block-start: 0;
      margin-block-end: 0;
    }
  }
  .Cards {
    display: grid;
    grid-template-columns: 1fr 0.5fr 1fr;
    grid-template-rows: 1fr;
    padding: 0 5vw;
    gap: 1vw;
    place-items: center;
  }
  .Page {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    width: 100%;
    background-size: cover;
    background-image: url("./orangebg2.jpg");
  }
  .progress-bar {
    width: 100%;
    min-width: 80px;
    height: 20px;

    background-color: rgba(224, 224, 224, 0.2);
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
      height: 100%;
    }
    .ScoreData {
      flex-direction: row;
      width: 100%;
      justify-content: space-between;
    }
    .progress-bar {
      max-width: 200px;
    }
  }
</style>
