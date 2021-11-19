<script lang="ts">
    import { onMount } from 'svelte';

    let currentProblem = '';
    let currentAnswer = 0;
    let streak = 0;
    let score = 0;

    function generateProblem() {
        const firstRand = Math.floor(Math.random() * 11);
        const secondRand = Math.floor(Math.random() * 11);

        if (firstRand === 0 || secondRand === 0 || Math.random() > 0.5) {
            currentProblem = `${firstRand} * ${secondRand}`;
            currentAnswer = firstRand * secondRand;
        } else {
            let divider = Math.random() > 0.5 ? firstRand : secondRand;

            currentProblem = `${firstRand * secondRand} / ${divider}`;
            currentAnswer = (firstRand * secondRand) / divider;
        }
    }

    generateProblem();

    onMount(() => {
        const form = document.getElementById('form');
        const input = document.getElementById('answer') as HTMLInputElement;

        form.addEventListener('submit', (e) => {
            e.preventDefault();

            if (parseInt(input.value) === currentAnswer) {
                score++;
                streak++;
            } else {
                streak = 0;
            }

            input.value = '';
            generateProblem();
        });
    });
</script>

<main>
    <section class="stats">
        <h2>Score: {score}</h2>
        <h2>Streak: {streak}</h2>
    </section>
    <form id="form">
        <h1 class="display">What is {currentProblem}?</h1>
        <input type="text" name="answer" id="answer" />
    </form>
</main>

<style>
    .stats {
        display: inline-block;
        position: absolute;
        background-color: #202830;
        padding: 20px 40px;
        top: 10px;
        left: 10px;
        border-radius: 5px;
    }

    main {
        display: grid;
        height: 100%;
    }

    form {
        margin: auto;
    }

    input {
        margin-top: 10px;
        border: none;
        padding: 20px;
        border-radius: 5px;
        font-size: 18px;
    }
</style>
