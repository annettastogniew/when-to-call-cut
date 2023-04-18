<script>
  export let shows;
  export let rowIndex;
  export let showDetails;
  export let disabled;
  import ShowCard from "./ShowCard.svelte";

</script>

<div class="row-container">
  {#each shows as show, i}
    <ShowCard disabled={disabled} show={show} tabIndex={4 + (rowIndex * 3) + i} showDetails={showDetails}/>
  {/each}
</div>

<style>
  .row-container {
    display: flex;
    justify-content: space-evenly;
    margin: 0.5em 0;
  }
</style>