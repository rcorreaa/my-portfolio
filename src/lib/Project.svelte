<script>
    export let data = {};
    export let hLevel = 2;
</script>

<article>
    <!-- <h2>{data.title}</h2> -->
    <svelte:element this={"h" + hLevel}>{data.title}</svelte:element>
    <img src={data.image} alt="" />
    <p>
        {data.description}
    </p>
</article>

<style>
    article{
        margin: 10px; /*step 4.3*/
        display: grid; /*step 4.4*/
        grid-template-rows: subgrid; /*step 4.4*/
        grid-row: span 3; /*step 4.4*/
    }

    article h2{
        margin: 0; /*step 4.4*/
    }
    p {
        font-size: 0.7em;
    }
    img {
        height: 12em;
    }
</style>