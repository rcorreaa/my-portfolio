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
    p {
        font-size: 0.7em;
    }
    img {
        height: 12em;
    }
</style>