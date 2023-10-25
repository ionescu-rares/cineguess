<script>
  import Button from "@smui/button";
</script>

<slot />

<style>
  @import url("https://fonts.googleapis.com/css2?family=Poppins&display=swap");

  :global(:root) {
    --background: #121212;
    --orange: #ff3e00;
    --surface: #757575;
    --dark-surface: #424242;
    --text-color: #ffffff;
    --dark-orange: #e63500;
    background-color: var(--background);
    color: var(--text-color);
    --mdc-theme-primary: var(--orange);
    --mdc-theme-on-primary: var(--text-color);
  }

  :global(body) {
    margin: 0;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: "Poppins", serif;
  }

  .mdc-button.MainButton {
    transition: background-color 0.3s;
  }

  /* Other global styles can go here */
</style>
