<script>
  import { createEventDispatcher } from "svelte";
  import * as d3 from 'd3';

  export let questionText;
  export let options;
  export let group;
  export let questionID;
  let disabled = false;

  const dispatch = createEventDispatcher();

  const logChoice = (event, choice) => {
    if (!disabled) {
      d3.selectAll('.'+questionID+'-radio')
        .filter(function() {
          return this.id !== event.target.id;
        })
        .each(function() {
          this.disabled = true;
        });

      dispatch('logChoice', {
        text: choice
      });
      
      disabled = true;
    }
  }

</script>

<div class="question-container hidden" id={questionID} aria-live="polite">
  <p class='centered'><strong>{questionText}</strong></p>
  <div class="options-container">
    {#each options as option, i}
      <label>
        <input 
          on:keydown={() => logChoice(event, Object.keys(option)[0])} 
          class={questionID+'-radio'} 
          id={questionID+'-radio-'+i} 
          type=radio bind:group={group} 
          name={group} 
          value={Object.keys(option)[0]} 
          on:click={(event) => logChoice(event, Object.keys(option)[0])}
          aria-label="Select option"
        >
        {Object.values(option)[0]}
      </label>
    {/each}
  </div>
</div>

<style>
  .options-container {
    display: flex;
    justify-content: space-evenly;
    margin: 1em 0;
  }

  .question-container {
    margin: 5em 0;
  }

  .centered {
    text-align: center;
  }

  .hidden {
    display: none;
  }

  label {
    font-size: 1.25em;
  }
</style>
