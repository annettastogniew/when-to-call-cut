<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  export let critic;
  export let showData;
  export let chartID;

  const getRatings = (data, critic) => {
    const result = []
    const cols = Object.keys(data);
    for (let i = 0; i < cols.length; i++) {
      const thisCol = cols[i];
      if (data[thisCol]) {
        if (critic) {
            const thisDatum = {};
            if (thisCol[0] === 's' && thisCol[thisCol.length - 1] === 't') {
              thisDatum.rating = data[thisCol];
              const intString = thisCol.substring(thisCol.indexOf('s')+1, thisCol.indexOf('_'));
              thisDatum.seasonNumber = parseInt(intString);
              result.push(thisDatum);
            }
        } else {
          const thisDatum = {};
            if (thisCol[0] === 's' && thisCol[thisCol.length - 1] === 'a') {
              thisDatum.rating = data[thisCol];
              const intString = thisCol.substring(thisCol.indexOf('s')+1, thisCol.indexOf('_'));
              thisDatum.seasonNumber = parseInt(intString);
              result.push(thisDatum);
            }
        }
      }
    }
    return result;
  }

  const ratingsChart = (ratingData, critic) => {
    const divWidth = d3.select('#intro').node().getBoundingClientRect().width;
    const mobile = divWidth <= 481;
    const manySeasons = showData['name'] === 'The Walking Dead' || showData['name'] === 'Brooklyn Nine-Nine';
    const toolTipText = mobile || manySeasons ? 's' : 'Season ';
    const chartHeight = mobile || manySeasons ? 110 : 75;
    const numSeasons = ratingData.length;
    const margin = {top: 30, right: -80, bottom: 70, left: mobile || manySeasons ? 0 : 140},
        width = divWidth - margin.left - margin.right,
        height = chartHeight - margin.top - margin.bottom;
    const calculatedRectMargin = (width / numSeasons / 10);
    const calculatedRectWidth = mobile ? (width - (calculatedRectMargin * (numSeasons - 1)) * 3.5) / numSeasons : 
    manySeasons ? (width - (calculatedRectMargin * (numSeasons - 1)) * 2.5) / numSeasons : 
    (width - (calculatedRectMargin * (numSeasons - 1))) / numSeasons;
    const rectMargin = Math.min(calculatedRectMargin, 10);
    const rectWidth = Math.min(calculatedRectWidth, 70);

    const mobileToolTip = seasonNumber => {
      const extraMargin = mobile ? rectWidth : 30;
      if (seasonNumber === 1) {
        return xCoord(seasonNumber)+extraMargin;
      } else if (seasonNumber === numSeasons && mobile) {
        return xCoord(seasonNumber)-extraMargin;
      } else {
        return xCoord(seasonNumber)+(rectWidth/2);
      }
    }

    const xCoord = seasonNumber => {
      if (seasonNumber === 1) {
        return 0;
      } else {
        return ((seasonNumber - 1) * rectWidth) + ((seasonNumber - 1) * rectMargin);
      }
    };

    const svg = d3.select('#'+chartID)
      .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

    const title = svg.append("text")
      .attr("class", "title")
      .attr("y", mobile || manySeasons ? 0 : 15)
      .style("font-size", "18px")
      .attr("font-weight", 700)
      .attr("fill", '#ffffff');

    if (critic) {
      title.text("Critic Scores")
        .attr("x", mobile || manySeasons ? 0 : -107);
    } else {
      title.text("Audience Scores")
        .attr("x", mobile || manySeasons ? 0 : -140);
    }
    
    const myColor = d3.scaleLinear().domain([0, 1])
    .range(["#FF3131", "#32CD32"]);
    
    const tooltip = svg.append("text")
      .style("opacity", 0)
      .attr("class", "tooltip")
      .style("fill", "#ffffff")
      .attr('text-anchor', 'middle');

      const mouseover = function(event, d) {
        tooltip
          .attr("x", mobile || manySeasons ? mobileToolTip(d.seasonNumber) : xCoord(d.seasonNumber)+(rectWidth/2))
          .attr("y", mobile || manySeasons ? 25 : -10)
          .style("opacity", 1)
      }
      const mousemove = function(event, d) {
        tooltip
          .text("Score: " + (d.rating * 100).toFixed(0) + "%")
      }
      const mouseleave = function(event, d) {
        tooltip
          .style("opacity", 0)
      }
    
    
    svg.append("g")
      .selectAll("rect")
      .data(ratingData)
      .join("rect")
        .attr("class", "season")
        .attr("width", rectWidth)
        .attr("height", 20)
        .attr("x", d => xCoord(d.seasonNumber))
        .attr("y", mobile || manySeasons ? 35 : 0)
        .attr("alt", d => critic ? "Critic " : "Audience " + "score for season " + d.seasonNumber + " was " + (d.rating * 100) + "%.")
        .style("fill", d => d.rating !== 'No Data' ? myColor(d.rating) : '#D3D3D3')
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave);
      
    
    svg.append("g")
      .selectAll("text")
      .data(ratingData)
      .join("text")
        .attr("class", "season-label")
        .attr("x", d => xCoord(d.seasonNumber)+(rectWidth/2))
        .attr("y", mobile || manySeasons ? 75 : 40)
        .text(d => d.seasonNumber === 1 ? toolTipText + d.seasonNumber : toolTipText  +d.seasonNumber)
        .style("text-anchor", "middle")
        .style("fill", "#ffffff");
  }

  const dataToChart = getRatings(showData, critic);

  onMount(() => {
    ratingsChart(dataToChart, critic);
  });
</script>

<figure id={chartID} class="chart">
</figure>

<style>
  @font-face {
    font-family: roboto;
    src: url('https://cdn.glitch.global/48adc8ae-4550-4ad0-bc51-caa00cbbb8f8/Roboto-Regular.ttf?v=1681482820467');
  }

  .chart {
    font-family: roboto;
    font-size: 12px;
    margin: 0.5em 0;
    width: 100%;
  }
</style>