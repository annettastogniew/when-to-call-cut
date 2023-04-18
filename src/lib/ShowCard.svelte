<script>
  export let show;
  export let showDetails;
  export let tabIndex;
  export let disabled;

  const handleKeyDown = event => {
    if(event.keyCode === 13){
      (!disabled) && showDetails(show)
    } 
  }
</script>

<div class="show-card" role="button" on:click={() => (!disabled) && showDetails(show)} tabindex={tabIndex} on:keydown={handleKeyDown}>
  <img id={show.pic.substring(0, show.pic.length-4) + "-img"} class="show-img" src={"public/assets/" + show.pic} alt={show.name + " title card"} aria-label="Select show" />
</div>

<style>

  @media screen and (max-width: 480px) {
    .show-img {
      width: 22vw;
    }
  }

  @media screen and (min-width: 480px) {
    .show-img {
      width: 10vw;
    }
  }
  .show-card {
    cursor: pointer;
  }

  .show-img:hover {
    border-color: var(--tertiary-color);
  }
  
  .show-img {
    border: 1.5px solid transparent;
  }
</style>