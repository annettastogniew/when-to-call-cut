# When To Call Cut
This project was created as a final project for Coding For Digital Storytelling at Northeastern University.

It was created using the [Vite](https://vitejs.dev/guide/) starter code for a Svelte app.

## Live Site
This project can be viewed at [https://annettastogniew.github.io/when-to-call-cut/](https://annettastogniew.github.io/when-to-call-cut/).

## Run Locally
To run this website locally, clone the [repo](https://github.com/annettastogniew/when-to-call-cut.git) and run `npm install`.

After installing all needed dependencies, run `npm run dev` and the site should be available at [http://localhost:5173/when_to_call_cut/](http://localhost:5173/when_to_call_cut/).

## Methods
Data for this project was sourced from [Is it cut short?](https://github.com/xdpirate/isitcutshort.com/blob/main/data.json), [Rotten Tomatoes](https://www.rottentomatoes.com/) and the [TMDB API](https://www.themoviedb.org/documentation/api?language=en-US). A list of 12 shows in four different categories - ran for too long, was cancelled too soon, ended at just the right time, was cancelled but revived - were sourced from various online lists:

- [Too short](https://www.reddit.com/r/television/comments/10thkq1/what_tv_shows_were_cancelled_far_too_soon_but/)
- [Too short](https://www.reddit.com/r/television/comments/ll129o/great_shows_that_got_cancelled_too_soon_before_a/)
- [Too short](https://www.reddit.com/r/AskReddit/comments/w5t67f/what_shows_got_canceled_too_soon/)
- [Too long](https://www.buzzfeed.com/shylawatson/tv-shows-that-went-on-too-long-reddit)
- [Revived](https://www.insider.com/fans-saved-tv-show-2018-6#jericho-fans-sent-thousands-of-pounds-of-nuts-to-cbs-to-get-the-network-to-renew-the-series-7)
- [Just right](https://www.buzzfeed.com/jennaguillaume/tv-shows-that-ended-at-the-right-time)

These classifications were cross-referenced with each show's ratings over time. If ratings were reflective of the classification, it was considered
reasonable and the show was viable for inclusion. 

Each show's Rotten Tomatoes ratings by season were collect by hand. TMDB voter scores and popularity scores were collected using the API. Show title
cards were also sourced from the API. TMDB data for shows from the Is it cut short? database were collected for comparison with shows used in the story.
