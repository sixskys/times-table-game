<script lang="ts">
    import { onMount } from 'svelte';

    let currentProblem = '';
    let currentAnswer = 0;
    let attemptedProblem = false;

    let streak = 0;
    let right = 0;
    let wrong = 0;

    function generateMultiplicatonProblem(firstInt: number, secondInt: number) {
        const random = Math.random();

        if (random >= 0.75) {
            return `${firstInt} * ${secondInt}`;
        } else if (random >= 0.5) {
            return `${firstInt} × ${secondInt}`;
        } else if (random >= 0.25) {
            return `${firstInt} <span class='multiplication-dot' aria-label='times'></span> ${secondInt}`;
        } else {
            return `${firstInt}(${secondInt})`;
        }
    }

    function generateDivisionProblem(product: number, divider: number) {
        const random = Math.random();

        if (random > 0.5) {
            return `${product} / ${divider}`;
        } else {
            return `${product} ÷ ${divider}`;
        }

        // can't get long division and fraction symbol to work

        // else if (random >= 0.25) {
        //     return `${divider}<span class='long-division' aria-label=' divided by '>${product}</span>`;
        // }
    }

    function generateProblem() {
        const firstRand = Math.floor(Math.random() * 11);
        const secondRand = Math.floor(Math.random() * 11);

        if (firstRand === 0 || secondRand === 0 || Math.random() > 0.5) {
            currentProblem = generateMultiplicatonProblem(
                firstRand,
                secondRand
            );

            currentAnswer = firstRand * secondRand;
        } else {
            let divider = Math.random() > 0.5 ? firstRand : secondRand;

            currentProblem = generateDivisionProblem(
                firstRand * secondRand,
                divider
            );

            currentAnswer = (firstRand * secondRand) / divider;
        }
    }

    generateProblem();

    onMount(() => {
        const form = document.getElementById('form');
        const input = document.getElementById('answer') as HTMLInputElement;

        input.addEventListener('animationend', (ev) => {
            input.classList.remove('shake');
        });

        form.addEventListener('submit', (ev) => {
            ev.preventDefault();

            const isCorrect = parseInt(input.value) === currentAnswer;

            if (!attemptedProblem && isCorrect) {
                right++;
                streak++;
            } else if (!attemptedProblem) {
                streak = 0;
                wrong++;
            }

            if (isCorrect) {
                input.value = '';
                attemptedProblem = false;
                generateProblem();
            } else {
                attemptedProblem = true;
                input.classList.add('shake');
            }
        });
    });
</script>

<main>
    <section class="stats">
        <h2>Score: {right}/{wrong + right}</h2>
        <h2 class:on-streak={streak > 0}>
            Streak: {streak}{streak > 0 ? ' 🔥' : ''}
        </h2>
    </section>
    <form id="form">
        <h1 class="display">What is {@html currentProblem}?</h1>
        <input
            type="number"
            name="answer"
            id="answer"
            class:is-wrong={attemptedProblem}
        />
    </form>
</main>

<style>
    .stats {
        display: inline-block;
        position: absolute;
        top: 10px;
        left: 10px;
    }

    .is-wrong {
        background-color: #ce2727;
        color: white;
    }

    .on-streak {
        background-color: #ce2727;
        transition: all 0.2s;
    }

    h1 {
        display: flex;
        align-items: center;
    }

    h2 {
        background-color: #2a4b7c;
        padding: 20px 30px;
        margin-bottom: 5px;
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
