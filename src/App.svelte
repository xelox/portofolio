<script lang="ts">
    import { Router, Link, Route } from "svelte-routing";
    import Greeting from "./lib/Greeting.svelte";
    import Background from "./lib/Background.svelte";
    import AboutMe from "./lib/AboutMe.svelte";
    import Projects from "./lib/Projects.svelte";
    import Contact from "./lib/Contact.svelte";
</script>

<main>
    <Background />
    <Greeting />
    <AboutMe />
    <Projects />
    <Contact />
</main>


<style>
    main{
        color: var(--text);
        position: fixed;
        top: 0;
        background-color: #222;
        height: 100vh;
        width: 100vw;
        overflow: scroll;
        scroll-behavior: smooth;
        scroll-snap-type: y mandatory;
    }
</style>