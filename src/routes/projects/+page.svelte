<svelte:head>
  <title>My projects</title>
</svelte:head>
<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
</script>

<!-- <pre>{ JSON.stringify(projects, null, "\t") }</pre>   -->

<h1>{ projects.length} Projects</h1>
<div class="projects">
    {#each projects as p}
        <Project data={p}/>
      <!-- <article>
        <h2>{p.title}</h2>
        <img src={p.image} alt="" />
        <p>
            {p.description}
        </p>
      </article> -->
    {/each}
</div>

<style>
    .projects {
	display:grid; /*step 4.3*/
	grid-template-columns : repeat(auto-fill, minmax(15em, 1fr));/*step 4.3*/
	min-width: 15em; /*step 4.3*/
	max-width: 1fr; /*step 4.3*/
}
</style>