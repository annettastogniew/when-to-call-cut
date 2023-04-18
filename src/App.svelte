<script>
  import IconButton, { Icon } from '@smui/icon-button';
  import { fade } from 'svelte/transition';
  import { onMount } from 'svelte';
  import Grid from './lib/Grid.svelte';
  import Question from './lib/Question.svelte';
  import RtChart from './lib/RTChart.svelte';
  import ShowSlider from './lib/ShowSlider.svelte';
  import * as d3 from 'd3';

  let showImage = false;
  let disableCards = false;
  let questionCount = 1;
  let shows = [];
  let revivedShows = [];
  let selectedShow = {};
  let selectedEnding = '';
  let selectedRevival = '';
  let question1Text = '';
  let question2Text = '';
  let question1Options = [{'at just the right time': 'well-timed'}, {'too soon': 'out of nowhere'}, {'past its prime': 'long overdue'}];
  let question2Options = [{'would': 'definitely'}, {'would not': 'no way'}];
  let endingText = '';
  let revivalText = '';
  let followingText = '';
  let wouldBeRevivedText = '';
  let compareText = '';
  const avgVoteRevived = 7.379273;
  const avgPopRevived = 33.017455;
  const avgVoteNotRevived = 7.093821;
  const avgPopNotRevived = 21.220151;

  const showStatic = (nextElement, reload, chapter) => {
    if (chapter === 2) {
      d3.select('#play-button').node().disabled = true;
    } else if (chapter === 3) {
      d3.select('#fast-forward-button').node().disabled = true;
      d3.select('#footer').node().classList.remove('hidden');
    }
    showImage = true;
    setTimeout(() => {
      if (reload) {
        window.location.reload();
      } else {
        document.getElementById(nextElement).classList.remove('hidden');
        document.getElementById(nextElement).scrollIntoView();
      }
    }, 950);
    setTimeout(() => {
      showImage = false;
    }, 750);
  };

  const setText = event => {
    if (questionCount === 2) {
      selectedEnding = event.detail.text;
      endingText = selectedEnding === selectedShow.cat ? 'Most people agree.' : 'Most people actually think it ended ' + selectedShow.cat + '.';
      nextQuestion();
    } else {
      selectedRevival = event.detail.text;
      revivalText = selectedRevival === 'would' ? "That's not suprising." : "Interesting. Some super fans might not agree.";
      followingText = selectedShow.following ? "does" : "does not";
      wouldBeRevivedText = wouldBeRevived(selectedShow.following, selectedShow.tmdb_vote, selectedShow.tmdb_pop) ? "could" : "could not";
      showStatic('results-content', false);
    }
  }

  const nextQuestion = () => {
    questionCount += 1;
    const nextQuestion = document.getElementById('question-' + questionCount);
    nextQuestion.classList.remove('hidden');
    nextQuestion.scrollIntoView();
  };

  const showDetails = show => {
    d3.selectAll('.show-card')
      .attr("tabindex", "-1");
    d3.selectAll('.show-img')
      .style('cursor', 'auto')
      .style('border-color', '#00000000')
      .attr("disabled", "disabled")
      .filter(function() {
        return this.id !== show.pic.substring(0, show.pic.length-4) + "-img";
      })
      .style('opacity', 0.25);

    nextQuestion();
    selectedShow = show;
    question1Text = selectedShow.done ? "First, tell us a little bit about " + selectedShow.name + ". What did you think of " + selectedShow.name + "'s ending?" :
    "First, tell us a little bit about " + selectedShow.name + ". What do you think of " + selectedShow.name + "'s incoming ending?";
    question2Text = "And be honest, if you could bring back " + selectedShow.name + ", would you?";

    disableCards = true;
  };

  const compareStat = (individual, average) => {
    const higher = individual >= average;
    return higher;
  }

  const setCompareText = (highVote, highPop) => {
    if (highVote && highPop) {
      compareText = 'both greater than';
    } else if (highVote) {
      compareText = 'respectively greater than and less than';
    } else if (highPop) {
      compareText = 'respectively less than and greater than';
    } else {
      compareText = 'both less than';
    }
  }

  const wouldBeRevived = (following, vote, popularity) => {
    const highVote = compareStat(vote, avgVoteRevived);
    const highPop = compareStat(popularity, avgPopRevived);
    setCompareText(highVote, highPop);
    return highVote && highPop && following;
  }

  onMount(async () => {
		const res = await fetch('data/show_ratings.json');
		shows = await res.json();

    const res2 = await fetch('data/revived_shows.json');
    revivedShows = await res2.json();
	});
</script>

<header id="intro" class="fullscreen-section">
  <div>
    <h1>
      When To Call Cut
    </h1>
    <h2>
      The stories of TV shows that were cut short, aired for one too many seasons or were revived post-cancellation 
    </h2>
    <p class="byline">By Annetta Stogniew</p>
    <IconButton id="play-button" on:click={() => showStatic('questions-content', false, 2)} color="secondary" aria-label="Move on">
      <Icon class="material-icons">play_arrow</Icon>
    </IconButton>
  </div>
</header>
<main>
  {#if showImage}
    <img transition:fade src="img/tv_static.gif" alt="tv static" class="fullscreen" id="tv-static">
  {/if}
  <section id="questions-content" class="hidden" aria-live="polite">
    <div id="question-1">
      <p>
        In the post-cable age, many seemingly ancient shows have seen resurgences in popularity. Some shows anchored
        such a fervent fanbase that fans actually managed to 
        <a href="https://www.insider.com/fans-saved-tv-show-2018-6">bring the show back</a>. Other shows have not been so fortunate.
      </p>
      <p>
        We've gathered some shows that were brutally cancelled, others that probably should've 
        ended in their prime and a few with perfectly-executed story archs.
      </p>
      <p>
        <strong>
          Select your favorite show from the list below to learn more about its ending and its potential for revival.
        </strong>
      </p>
      {#if shows.length > 0}
        <Grid bind:disabled={disableCards} shows={shows} showDetails={showDetails}/>
      {/if}
    </div>
    <Question questionID="question-2" questionText={question1Text} options={question1Options} group={selectedEnding} on:logChoice={setText} />
    <Question questionID="question-3" questionText={question2Text} options={question2Options} group={selectedRevival} on:logChoice={setText} />
  </section>
  {#if showImage}
    <img transition:fade src="img/tv_static.gif" alt="tv static" class="fullscreen" id="tv-static-2">
  {/if}
  <section id="results-content" class="hidden" aria-live="polite">
    {#if endingText && revivalText}
        <p id="results-top">You rated {selectedShow.name} as having ended {selectedEnding}. {endingText}</p>
        <p bind:innerHTML={selectedShow.desc} contenteditable="false"></p>
        <p bind:innerHTML={selectedShow.details} contenteditable="false"></p>
        <p>
          Here is a breakdown of {selectedShow.name}'s <a href={selectedShow.rt_link}>Rotten Tomatoes</a> ratings by season.
          As you can see, {selectedShow.name}'s Rotten Tomatoes scores {selectedShow.trend}.
        </p>
        <div class="charts">
          <RtChart critic={true} showData={selectedShow} chartID='selected-critic-chart' />
          <RtChart critic={false} showData={selectedShow} chartID='selected-audience-chart'/>
        </div>
        <p>
          Given the chance, you said you {selectedRevival} bring back {selectedShow.name} if you could. Compared to other shows that have been
          revived, {selectedShow.name} probably {wouldBeRevivedText} be revived if fans put in the effort.
        </p>
        <p>
          The most obvious qualifier for a show to be revived is whether or not the ending satisfied viewers. <a href="https://www.isitcutshort.com/">Is it cut short?</a> 
          is a database of shows that ended on a cliffhanger, were cancelled before the show's intended ending or ended in a way that was generally upsetting to fans. 
          Of the 543 shows in the database, only 11 - or about 2.05% - were revived. 
        </p>
        <p>
          Clearly revival is a pretty rare opportunity for cancelled TV shows. So how did those 11 shows make it happen?
        </p>
        <p>
          Looking at the numbers, those revived shows had an average <a href="https://www.themoviedb.org/?language=en-US">TMDB</a> 
          voter score of {avgVoteRevived.toFixed(2)}%, compared to {avgVoteNotRevived.toFixed(2)}% for shows that
          were not revived. The revived shows also had an average TMDB popularity score of {avgPopRevived.toFixed(2)}, compared to {avgPopNotRevived.toFixed(2)}
          for non-revived shows.
        </p>
        <p>
          {selectedShow.name} has a TMDB voter score of {selectedShow.tmdb_vote.toFixed(2)}% and a TMDB popularity scoe of {selectedShow.tmdb_pop.toFixed(2)}. Those are
          {compareText} the averages for revived shows. 
        </p>
        <p>
          But beyond the numbers, a key factor in a show's revival seems to be a cult fanbase willing to go to extremes to bring back their favorite series.
          {selectedShow.name} {followingText} seem to have this sort of following.
        </p>
        <p>
          Although {selectedShow.name} has a dedicated fanbase, executives from revived shows faced especially fervent fanatics after cancellation.  
        </p>
        <p>
          <strong>Click the button below to learn about the extremes to which some fans went to bring back a favorite show.</strong>
        </p>
        <div class="bottom-button">
          <IconButton id="fast-forward-button" on:click={() => showStatic('revived-content', false, 3)} color="secondary" aria-label="Move on">
            <Icon class="material-icons">fast_forward</Icon>
          </IconButton>
        </div>
    {/if}
  </section>
  {#if showImage}
    <img transition:fade src="img/tv_static.gif" alt="tv static" class="fullscreen" id="tv-static-3">
  {/if}
  <section id="revived-content" class="not-fullscreen hidden" aria-live="polite">
    {#if revivedShows.length > 0}
      <ShowSlider bind:revivedShows={revivedShows} />
    {/if}
    <p>
      Aside from revivals, shows with dissatisfying endings can find come back in other ways. Some of the shows from Is it cut short?
      received closure through reboots within the same franchise or fictional universe, movies, comics, books or leaked endings.
    </p>
    <p>
      And sometimes, not knowing is better than enduring a subpar remake. Shows that garnered a strong enough fanbase to find 
      dissapointment in their ending surely must have had some moments to cherish.
    </p>
    <div class="bottom-button">
      <IconButton class="text-icon-button" on:click={() => showStatic('revived-content', true, 1)} color="secondary" aria-label="Refresh page">
        <Icon class="material-icons">fast_rewind</Icon>
        Back to start
      </IconButton>
    </div>
  </section>
</main>
<footer id="footer" class="hidden" aria-live="polite">
  <p>
    This project was created by <a href="https://twitter.com/a_stogniew">Annetta Stogniew</a> for Coding For Digital Storytelling 
    at Northeastern University. It uses data from <a href="https://github.com/xdpirate/isitcutshort.com/blob/main/data.json">Is it cut short?</a>, 
    <a href="https://www.rottentomatoes.com/">Rotten Tomatoes</a> and the <a href="https://www.themoviedb.org/documentation/api?language=en-US">TMDB API</a> 
    to analyze TV shows' ratings over time. 
  </p>
</footer>

<style>

  @media screen and (max-width: 480px) {
    header {
      width: 80vw;
    }

    main {
      width: 80vw;
    }

    footer {
      width: 80vw;
    }

    .bottom-button {
      margin: 1em 0 4em;
    }
  }

  @media screen and (min-width: 480px) {
    header {
      max-width: 50vw;
    }

    main {
      width: 50vw;
    }

    footer {
      width: 50vw;
    }
  }
  section {
    margin: auto;
  }

  header {
    display: flex;
    justify-content: center;
    flex-direction: column;
  }

  footer {
    margin-top: 6em;
  }

  .charts {
    margin: 1em 0;
  }
  .fullscreen-section {
    height: 100vh;
  }

  .fullscreen {
    height: 100vh;
    width: 100vw;
    position: fixed;
    right: 0;
    top: 0;
  }
  .hidden {
    display: none;
  }
  .bottom-button {
    margin-bottom: 4em;
  }
  .byline {
    text-align: center;
  }
</style>
