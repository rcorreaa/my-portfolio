<svelte:head>
  <title>Mike Wazowski: Personal site and portfolio</title>
</svelte:head>
<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
</script>
<h1> Mike Wazowski</h1>
   
<img src="./images/mike.png" alt="mike" width="500px">

<p>Michael "Mike" Thomas Wazowski is the deuteragonist of the 2001 Disney Pixar animated film Monsters, Inc. and the protagonist of its 2013 prequel.
    Depicted as a diminutive, one-eyed monster with a wisecracking veneer, Mike is an employee of Monsters, Incorporated, where he works closely with his longtime partner/best friend Sulley.
    Mike's world gets turned upside down when a human girl (nicknamed "Boo") enters the monster world.
    Teaming up with Sulley to return Boo to her world, Mike uncovers a company conspiracy and helps solve an energy crisis that plagues the entire city of Monstropolis
</p>
<h2>
  Latest Projects
</h2>
<div class="projects">
{#each projects.slice(0, 3) as p}
  <Project data={p} hLevel="3"/>
{/each}
</div>
