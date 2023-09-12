<script lang="ts">
    import { Router, Link, Route } from "svelte-routing";
    import Greeting from "./lib/Greeting.svelte";
    import Background from "./lib/Background.svelte";
</script>

<main>
    <Background />
    <Router>
        <Route path="/greetings" component={Greeting} />
    </Router>
</main>
