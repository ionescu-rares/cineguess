<script src="script.js">
  import Game from "../Game.svelte";
  import { movies } from "../movies";

  const slicedMovies = [...movies];
</script>

<div class="Page">
  <Game slicedContent={slicedMovies} mode="movies" />
</div>
