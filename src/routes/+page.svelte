<script>
  import { fade } from "svelte/transition";
  import Button from "@smui/button";
</script>

<div class="menu" in:fade={{ duration: 500 }}>
  <img src="cinelogo.png" width={300} height={300} alt="logo" />
  <h4>Choose mode</h4>
  <div class="options">
    <div class="menuButtonWrapper">
      <a href="/play/movies" class="Link">
        <Button variant="raised">movies</Button></a
      >
    </div>
    <div class="menuButtonWrapper">
      <a href="/play/series" class="Link"
        ><Button variant="raised">TV series</Button></a
      >
    </div>
  </div>
</div>

<style lang="scss">
  @import url("https://fonts.googleapis.com/css2?family=Poppins&display=swap");

  .menu {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    background-color: var(--surface);
    border-radius: 10px;
    padding-bottom: 24px;
  }
  .options {
    display: flex;
    gap: 24px;
    flex-direction: column;
    align-items: center;
    width: 100%;
    .Link {
      text-decoration: none !important;
    }
  }
  .menuButtonWrapper :global(.mdc-button) {
    width: 200px;
  }
</style>
