<script>
  import RtChart from "./RTChart.svelte";
  export let show;
  export let showChart;
</script>

<p bind:innerHTML={show.desc} contenteditable="false"></p>
<p bind:innerHTML={show.details} contenteditable="false"></p>
<p>
  Here is a breakdown of {show.name}'s <a href={show.rt_link}>Rotten Tomatoes</a> ratings by season.
  {show.name}'s Rotten Tomatoes scores {show.trend}.
</p>
{#if showChart}
  <div class="charts">
    <RtChart critic={true} bind:showData={show} chartID='revived-critic-chart' />
    <RtChart critic={false} bind:showData={show} chartID='revived-audience-chart' />
  </div>
{/if}
<p>
  {show.name} had a TMDB voter score of {show.tmdb_vote ? show.tmdb_vote.toFixed(2) : ''}% and 
  a popularity score of {show.tmdb_pop ? show.tmdb_pop.toFixed(2) : ''}.
</p>

<style>
  .charts {
    margin: 2.5em 0;
  }
</style>