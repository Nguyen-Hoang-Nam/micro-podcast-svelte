<script lang="ts">
    import Router from "svelte-spa-router";

    import Subscription from "./routes/Subscription.svelte";
    import Podcast from "./routes/Podcast.svelte";
    import Home from "./routes/Home.svelte";

    const routes = {
        // Exact path
        "/": Home,

        // Using named parameters, with last being optional
        "/subscription": Subscription,

        // Wildcard parameter
        "/podcast/:title": Podcast,

        // Catch-all
        // This is optional, but if present it must be the last
        /* '*': NotFound, */
    };
</script>

<Router {routes} />

<style>
</style>
