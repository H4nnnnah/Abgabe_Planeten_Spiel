<script>
    import Router from "svelte-spa-router";
    import Header from "./components/Header.svelte";
    import Quiz from "./components/Quiz.svelte";
    import Anleitung from "./components/Anleitung.svelte";

    const routes = {
        "/": Anleitung,
        "/quiz": Quiz,
    };
</script>

<div id="wrapper">
    <Header />
    <Router {routes} />
</div>

<style>
    #wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: auto;
        min-height: 100vh;
        width: 100vw;
        background-color: #042c51;
        color: white;
        font-family: "Arial", sans-serif;
    }
</style>
