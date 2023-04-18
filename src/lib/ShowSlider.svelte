<script>
  import IconButton, { Icon } from '@smui/icon-button';
  import ShowInfo from "./ShowInfo.svelte";
  import ShowImage from './ShowImage.svelte';

  export let revivedShows;

  let showIndex = 0;
  let currentShow = revivedShows[showIndex];
  let showChart = true;

  const nextShow = () => {
    showIndex += 1;
    currentShow = revivedShows[showIndex];
    showChart = false;
    setTimeout(() => {
      showChart = true;
    }, 500);
  }

  const prevShow = () => {
    showIndex = showIndex - 1;
    currentShow = revivedShows[showIndex];
    showChart = false;
    setTimeout(() => {
      showChart = true;
    }, 500);
  }
  
</script>

<div class="slider-container">
  <div class="slider-header" aria-live="polite">
    {#if showIndex > 0}
      <IconButton id="prev-button" on:click={prevShow} color="secondary" aria-label="Back to previous show">
        <Icon class="material-icons">arrow_back_ios</Icon>
      </IconButton>
    {:else}
      <div class="button-filler"></div>
    {/if}
    <ShowImage bind:show={currentShow} />
    {#if showIndex < revivedShows.length - 1}
      <IconButton id="next-button" on:click={nextShow} color="secondary" aria-label="Move on to next show">
        <Icon class="material-icons">arrow_forward_ios</Icon>
      </IconButton>
    {:else}
      <div class="button-filler"></div>
    {/if}
  </div>
  <ShowInfo bind:show={currentShow} showChart={showChart}/>
</div>

<style>

  @media screen and (max-width: 480px) {
    .slider-container {
      width: 85vw;
    }
  }
  .slider-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 2em;
  }

  .button-filler {
    width: 72px;
    height: 53px;
    margin: auto;
  }
</style>