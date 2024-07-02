<script>
    import Kategorie from "./Kategorie.svelte";
    import Aufgabe from "./Aufgabe.svelte";
    import { score } from "../store.js";
    import { onMount } from "svelte";

    let question = null;
    let planets = null;
    let selectedAnswer = null;
    let isCorrect = null;
    let askedQuestions = [];
    let showPopup = false;
    let finalScore = 0;

    onMount(async () => {
        console.log("ich bin gelaufen");
        planets = await fetch(
            "https://api.le-systeme-solaire.net/rest/bodies?filter[]=isPlanet,eq,true",
        )
            .then((response) => response.json())
            .then((data) => {
                return data.bodies;
            });
        console.log(planets);
        question = generateQuestion();
    });

    score.subscribe((value) => {
        finalScore = value; // Update the finalScore variable whenever score changes
        console.log(value);
    });

    const getMaxPlanet = (planets, key) => {
        return planets.reduce((maxPlanet, planet) => {
            return planet[key] > maxPlanet[key] ? planet : maxPlanet;
        });
    };

    const getMinPlanet = (planets, key) => {
        return planets.reduce((minPlanet, planet) => {
            return planet[key] < minPlanet[key] ? planet : minPlanet;
        });
    };

    function handleAnswerClick(answer) {
        selectedAnswer = answer;
        if (answer === question.correctAnswer) {
            isCorrect = true;
            console.log("correct");
            score.update((n) => n + 1);
            setTimeout(() => {
                question = generateQuestion();
                selectedAnswer = null;
                isCorrect = null;
            }, 800);
        } else {
            isCorrect = false;
            console.log("incorrect");
            score.update((n) => n - 1);
            setTimeout(() => {
                selectedAnswer = null;
                isCorrect = null;
            }, 800);
        }
    }

    const questionPatterns = [
        { wording: "ist der größte", key: "meanRadius", getMax: true },
        { wording: "ist der kleinste", key: "meanRadius", getMax: false },
        {
            wording: "ist der durchschnittlich sonnenentfernteste",
            key: "semimajorAxis",
            getMax: true,
        },
        {
            wording: "ist der durchschnittlich sonnenennächste",
            key: "semimajorAxis",
            getMax: false,
        },
        {
            wording: "hat die durchnittlich höchste Temperatur",
            key: "avgTemp",
            getMax: true,
        },
        {
            wording: "hat die durchnittlich niedrigste Temperatur",
            key: "avgTemp",
            getMax: false,
        },
        {
            wording: "dreht sich am langsamsten um sich selbst?",
            key: "sideralRotation",
            getMax: true,
        },
        {
            wording: "dreht sich am schnellsten um sich selbst?",
            key: "sideralRotation",
            getMax: false,
        },
        {
            wording: "hat die höchste Gravitation",
            key: "gravity",
            getMax: true,
        },
        {
            wording: "hat die niedrigste Gravitation",
            key: "gravity",
            getMax: false,
        },
        {
            wording: "brauch die längste Zeit um die Sonne zu umrunden",
            key: "sideralOrbit",
            getMax: true,
        },
        {
            wording: "brauch die kürzeste Zeit um die Sonne zu umrunden",
            key: "sideralOrbit",
            getMax: false,
        },
    ];

    let lastRandomNumber = -1; // Variable to store the last random number

    const generateQuestion = () => {
        if (askedQuestions.length === questionPatterns.length) {
            // If all questions have been asked, show the popup

            showPopup = true;
            return null;
        }

        let currentRandomNumber;
        do {
            currentRandomNumber = Math.floor(
                Math.random() * questionPatterns.length,
            );
        } while (askedQuestions.includes(currentRandomNumber));

        askedQuestions.push(currentRandomNumber);
        const questionPattern = questionPatterns[currentRandomNumber];

        let relevantPlanet =
            questionPattern.getMax !== false
                ? getMaxPlanet(planets, questionPattern.key)
                : getMinPlanet(planets, questionPattern.key);

        return {
            question: `Welcher dieser Planeten ${questionPattern.wording}?`,
            answers: planets.map((planet) => planet.englishName),
            correctAnswer: relevantPlanet.englishName,
            key: questionPattern.key,
            isMax: questionPattern.getMax !== false,
        };
    };

    function resetGame() {
        askedQuestions = [];
        showPopup = false;
        question = generateQuestion();
        score.set(0);
    }
</script>

{#if !showPopup}
    <h2>{question && question.question}</h2>
    <p>die richtigte antwort ist {question && question.correctAnswer}</p>
{/if}

{#if question}
    <div class="grid-container">
        {#each question.answers as answer}
            <button
                class="answer-option {selectedAnswer === answer
                    ? isCorrect
                        ? 'correct'
                        : 'incorrect'
                    : ''}"
                on:click={() => handleAnswerClick(answer)}
            >
                {answer}
            </button>
        {/each}
    </div>
{/if}
<p>
    score: {$score}
</p>

{#if showPopup}
    <div class="popup">
        <h2>Toll gemacht!</h2>
        <p>final score: {finalScore}</p>
        <button on:click={resetGame}>Play Again</button>
    </div>
{/if}

<div class="planets-grid" style:display={showPopup ? "none" : "block"}>
    <img
        src="Uranus.png"
        alt="Uranus"
        class="uranus"
        style="position: absolute; top: 240px; left: 485px; transform: rotate(45deg);"
    />

    <img
        src="Neptune.png"
        alt="Neptune"
        class="neptune"
        style="position: absolute; top: 264px; left: 740px;"
    />
    <img
        src="Saturn.png"
        alt="Saturn"
        class="saturn"
        style="position: absolute; top: 484px; left: 723px; transform: rotate(25deg) "
    />
    <img
        src="Jupiter.png"
        alt="Jupiter"
        class="Jupiter"
        style="position: absolute; top: 375px; left: 485px"
    />
    <img
        src="Mars.png"
        alt="Mars"
        class="mars"
        style="position: absolute; top: 375px; left: 740px"
    />
    <img
        src="Erde.png"
        alt="Erde"
        class="earth"
        style="position: absolute;top: 595px; left: 485px;"
    />
    <img
        src="Merkur.png"
        alt="Merkur"
        class="mercury"
        style="position: absolute; top: 487px; left: 485px"
    />
    <img
        src="Venus.png"
        alt="Venus"
        class="venus"
        style="position: absolute; top: 594px; left: 740px"
    />
</div>

<style>
    .grid-container {
        display: grid;
        grid-template-columns: 1fr 1fr;
        grid-gap: 10px;
        column-width: 500px;
    }

    .answer-option {
        padding: 20px;
        background-color: rgb(255, 123, 0);
        border-radius: 20px;
        border: 4px solid rgb(255, 255, 255);
        text-align: center;
        cursor: pointer;
        width: 250px;
        height: 100px;
        font-size: 1.5rem;
        color: #042c51;
    }

    .answer-option.correct {
        border-color: rgb(99, 197, 120);
    }

    .answer-option.incorrect {
        border-color: #942916;
    }

    h2 {
        font-size: 2rem;
        margin-top: 3rem;
    }
    @media (max-width: 768px) {
        .grid-container {
            grid-template-columns: 1fr 1fr;
        }

        .answer-option {
            text-align: center;
            width: 100%;
            margin-bottom: 10px;
        }

        .planets-grid img {
            display: none; /* Bilder der Planeten ausblenden */
        }
        h2 {
            text-align: center;
        }
    }

    .popup {
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        text-align: center;
        background-color: rgb(255, 123, 0);
        border-radius: 20px;
        border: 4px solid rgb(255, 255, 255);
        cursor: pointer;
        width: 250px;
        height: 250px;
        font-size: 1.5rem;
        color: #042c51;
    }

    .popup h2 {
        font-size: 2rem;
        text-align: center;
    }

    .popup p {
        font-size: 1.5rem;
    }

    .popup button {
        padding: 10px 20px;
        font-size: 1.2rem;
        cursor: pointer;
        background-color: white;
        border-radius: 8px;
        border: 4px solid rgb(255, 255, 255);
        color: orange;
        width: 210px;
    }
</style>
