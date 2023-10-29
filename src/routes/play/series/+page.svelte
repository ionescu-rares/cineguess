<script>
  import Game from "../Game.svelte";
  import { series } from "../series";

  const slicedSeries = [...series];
</script>

<div class="Page">
  <Game slicedContent={slicedSeries} mode="series" />
</div>
