<script>
    import { page } from "$app/stores";
    import { base } from "$app/paths";

    let pages = [
        { url: "/", title: "Home" },
        { url: "/projects", title: "Projects" },
        { url: "/contact", title: "Contact" },
        { url: "/resume", title: "Resume" },
        { url: "/meta", title: "Meta" },
        {url: "https://github.com/Mike-Kowalski", title:"Github"}
    ];

    let colorScheme = "light dark";
    let root = globalThis?.document?.documentElement;
    $: root?.style.setProperty("color-scheme", colorScheme);

    let localStorage = globalThis.localStorage ?? {};
    $: localStorage.colorScheme = colorScheme;
</script>

<!-- {
    JSON.stringify($page)
} -->
  
<nav>
    {#each pages as p}
        <a
            href={p.url.startsWith("http") 
                ? p.url: `${base}${p.url}`}
            class:current={$page.route.id === p.url}
            target={p.url.startsWith("http") ? "_blank" : undefined}
        >
            {p.title}
        </a>
    {/each}
</nav>  

<label class="color-scheme">
    Theme:

    <select bind:value={ colorScheme }>
        <option value="light dark"> Automatic </option>
        <option value="light"> Light </option>
        <option value="dark"> Dark </option>
    </select>
</label>

<!-- { colorScheme } -->
<slot />

<style>
    nav {
        --border-color: oklch(50% 10% 200 / 40%);
        
        display: flex;
        margin-bottom: 1em;
        border-bottom-width:1px;
        border-bottom-style:solid;
        
        border-bottom-color: var(--border-color);
    }

    nav a {
        flex:1;
        text-decoration: none;
        color: inherit;
        text-align: center; 
        padding: 0.5em;
    }

    nav a.current {
        border-bottom-width:0.4em;
        border-bottom-style:solid;
        border-bottom-color: var(--border-color);
        padding-bottom:0.1em;
        font-weight: bold;
    }

    nav a:hover {
        border-bottom-width:0.4em;
        border-bottom-style:solid;
        border-bottom-color:var(--color-accent);
        padding-bottom:0.1em;
        background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
    }

    .color-scheme{
        position:absolute;
        top: 1rem;
        right: 1rem;
        display: inline-flex;
        gap: 5px;
    }
</style>