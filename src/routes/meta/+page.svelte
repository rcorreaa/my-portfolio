<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  import {
    computePosition,
    autoPlacement,
    offset,
  } from '@floating-ui/dom';
  import Bar from '$lib/Bar.svelte';
  import FileLines from "$lib/FileLines.svelte";
  import StackedBar from "$lib/StackedBar.svelte";
  import Scrolly from "svelte-scrolly";

  let data = [];
  let commits = [];
  let width = 1000, height = 600;
  let margin = {top: 10, right: 10, bottom: 30, left: 20};
  let hoveredIndex = -1;
  let hoveredCommit = -1;
  let cursor = {x: 0, y: 0};
  let commitTooltip;
  let tooltipPosition = {x: 0, y: 0};
  let clickedCommits = [];

  let commitProgress = 100;
  let raceProgress = 100;

  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let xAxis, yAxis;
  let yAxisGridlines;

  $: hoveredCommit = filteredCommits[hoveredIndex] ?? hoveredCommit ?? {};

  $: allTypes = Array.from(new Set(data.map(d => d.type)));
  $: selectedLines = (clickedCommits.length > 0 ? clickedCommits : filteredCommits).flatMap(d => d.lines);
  $: selectedCounts = d3.rollup(
    selectedLines,
    v => v.length,
    d => d.type
  );
  $: languageBreakdown = allTypes.map(type => [type, selectedCounts.get(type) || 0]);

  const colorScale = d3.scaleOrdinal(d3.schemeTableau10);

  $: minDate = d3.min(commits.map(d => d.date));
  $: maxDate = d3.max(commits.map(d => d.date));
  $: maxDatePlusOne = new Date(maxDate);
  $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

  $: timeScale = d3.scaleTime().domain([minDate,maxDatePlusOne]).range([0,100]);
  $: commitMaxTime = timeScale.invert(commitProgress);
  $: commitMaxTime_2 = timeScale.invert(raceProgress);

  $: filteredCommits = commits.filter( d=> d.datetime <= commitMaxTime);
  $: filteredData = data.filter( d=> d.datetime <= commitMaxTime_2);

  $: filteredMinDate = d3.min(filteredCommits.map(d => d.date));
  $: filteredMaxDate = d3.max(filteredCommits.map(d => d.date));
  $: filteredMaxDatePlusOne = new Date(filteredMaxDate);
  $: filteredMaxDatePlusOne.setDate(filteredMaxDatePlusOne.getDate() + 1);

  $: xScale = d3.scaleTime().domain([filteredMinDate, filteredMaxDatePlusOne]).range([usableArea.left, usableArea.right]).nice();
  $: yScale = d3.scaleLinear().domain([24, 0]).range([usableArea.bottom, usableArea.top]);
  $: rScale = d3.scaleSqrt().domain(d3.extent(commits.map(d => d.totalLines))).range([2, 30]);

  $: {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
    d3.select(yAxisGridlines).call(
      d3.axisLeft(yScale).tickFormat("").tickSize(-usableArea.width)
    );
  }

  $: techCounts = d3.rollups(filteredData, v => v.length, d => d.type);
  $: barData = Object.fromEntries(techCounts);

  async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;
    if (evt.type === "mouseenter") {
      hoveredIndex = index;
      cursor = {x: evt.x, y: evt.y};
      tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
        strategy: "fixed",
        middleware: [offset(5), autoPlacement()],
      });
    } else if (evt.type === "click") {
      let commit = filteredCommits[index];
      if (!clickedCommits.includes(commit)) clickedCommits = [...clickedCommits, commit];
      else clickedCommits = clickedCommits.filter(c => c !== commit);
    } else if (evt.type === "mouseleave") hoveredIndex = -1;
  }

  onMount(async () => {
    data = await d3.csv("./loc.csv", row => ({
      ...row,
      line: +row.line,
      depth: +row.depth,
      length: +row.length,
      date: new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime)
    }));

    commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let {author, date, time, timezone, datetime} = first;
      let ret = {
        id: commit,
        url: "https://github.com/juan1t0/my-portfolio/commit/" + commit,
        author, date, time, timezone, datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length
      };
      Object.defineProperty(ret, "lines", { value: lines, configurable: true, writable: true });
      return ret;
    });
    commits = d3.sort(commits, d => -d.totalLines);
  });
</script>

<Scrolly bind:progress={commitProgress}>
  {#each filteredCommits as commit, index}
    <p>
      On {new Date(commit.datetime).toLocaleString("en", {dateStyle: "full", timeStyle: "short"})},
      {index === 0 ? "the first commit kicked off our journey." : "another important update was made."}
      <a href={commit.url} target="_blank">View it here</a>. It changed {commit.totalLines} lines.
    </p>
  {/each}
  <svelte:fragment slot="viz">
    <StackedBar data={barData} width={width} colorScale={colorScale} />
  </svelte:fragment>
</Scrolly>

<Scrolly bind:progress={raceProgress}>
  {#each filteredCommits as commit, index}
    <p>
      By {new Date(commit.datetime).toLocaleDateString()}, the file structure had grown. Below is how file sizes evolved.
    </p>
  {/each}
  <svelte:fragment slot="viz">
    <FileLines lines={filteredData} width={0.7 * width} colorScale={colorScale} />
  </svelte:fragment>
</Scrolly>

<Bar data={languageBreakdown} width={width} />

<style>
  :global(body) {
    max-width: min(120ch, 80vw);
  }
</style>