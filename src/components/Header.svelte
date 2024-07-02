<script>
    import { location } from "svelte-spa-router";
</script>

<header>
    <h1>Planeten Spiel</h1>
    <nav>
        <a href="#/" class:active={$location === "#/"}>Spielanleitung</a>
        <a href="#/quiz" class:active={$location === "#/quiz"}>Jetzt spielen</a>
    </nav>
</header>

<style>
    .active {
        font-weight: bold;
        color: red;
    }
    header {
        background-color: #ffffff;
        color: rgb(255, 123, 0);
        display: flex;
        width: 100%;
        justify-content: space-between;
        align-items: center;
    }
    h1 {
        margin-left: 1rem;
    }

    nav {
        display: flex;
        margin-right: 1rem;
    }

    nav a {
        color: rgb(255, 123, 0);
        text-decoration: none;
        margin-left: 1rem;
    }

    nav a:hover {
        text-decoration: underline;
    }
</style>
