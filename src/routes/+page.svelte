<script>
  import Button from "@smui/button";
</script>

<div class="menu">
  <img src="cinelogo.png" width={300} alt="logo" />
  <h4>Choose mode</h4>
  <div class="options">
    <a href="/play/movies"> <Button variant="raised">movies</Button></a>
    <a href="/play/series"><Button variant="raised">TV series</Button></a>
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
    gap: 16px;
  }
  .options {
    display: flex;
    justify-content: space-around;
    width: 100%;
  }
</style>
