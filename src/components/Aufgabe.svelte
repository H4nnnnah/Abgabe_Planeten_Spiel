<script>
    export let answers;
</script>

<h2>Hier sol die Aufgabe stehen</h2>

<div class="answers-grid-container">
    {#each answers as answer}
        <div class="answer">
            {answer}
        </div>
    {/each}
</div>

<style>
    .answers-grid-container {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        grid-template-rows: repeat(2, 1fr);
        grid-gap: 10px;
        column-width: 500px;
    }

    .answer {
        background-color: #333;
        color: white;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>
