<script>
    import { onMount } from "svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { goto } from "$app/navigation";
    import { fly, scale, fade } from "svelte/transition";

    let quizies = $state([]);
    let isLoading = $state(true);
    let progress = $state(0);
    let interval = $state(null);
    let questionTimeLimit = $state(30);
    let timeRemaining = $state(30);
    let activeQuestion = $state(0);
    let answers = $state("");
    let showResult = $state(false);
    let results = $state({});
    let showScoreToast = $state(false);
    let lastAnswerCorrect = $state(false);

    function handleCalculate() {
        let correct = 0;
        let incorrect = 0;

        for (let index = 0; index < quizies.length; index++) {
            if (quizies[index]?.answer === quizies[index].correct_answer) {
                correct++;
            } else {
                incorrect++;
            }
        }

        results = { correct, incorrect, total: 10 };
        showResult = true;
    }

    function handleComposeQuiz() {
        const _quizies = $globalQuiz;

        quizies = _quizies.map((q) => {
            const options = [...q.incorrect_answers, q.correct_answer];

            for (let i = options.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [options[i], options[j]] = [options[j], options[i]];
            }

            return {
                question: q.question,
                options,
                correct_answer: q.correct_answer,
            };
        });

        isLoading = false;
        // Start timer for first question
        startQuestionTimer();
    }

    function handleNext() {
        if (activeQuestion >= 9) {
            handleCalculate();
            return;
        }

        // Check if answer is correct
        lastAnswerCorrect = answers === quizies[activeQuestion].correct_answer;

        // Show score toast
        showScoreToast = true;
        setTimeout(() => {
            showScoreToast = false;
        }, 1500);

        quizies[activeQuestion].answer = answers;
        answers = "";

        activeQuestion += 1;

        if (activeQuestion < quizies.length) {
            startQuestionTimer();
        }
    }

    function startQuestionTimer() {
        progress = 0;
        timeRemaining = questionTimeLimit;

        if (interval) {
            clearInterval(interval);
        }

        interval = setInterval(() => {
            if (timeRemaining > 0) {
                timeRemaining -= 0.1;
                progress = (1 - timeRemaining / questionTimeLimit) * 100;
            } else {
                clearInterval(interval);
                handleNext();
            }
        }, 100);
    }

    onMount(() => {
        isLoading = true;

        if ($globalQuiz.length > 0) {
            handleComposeQuiz();
        } else {
            goto("/");
        }

        return () => {
            if (interval) {
                clearInterval(interval);
            }
        };
    });
</script>

<div class="flex justify-between items-center pb-6 relative my-8 mx-4">
    <h2 class="text-2xl font-bold text-slate-800 font-outfit">
        Quiz Challenge
    </h2>
    <div class="flex items-center space-x-4">
        <div class="text-lg font-semibold text-slate-700 font-inter">
            Question {activeQuestion + 1}/10
        </div>
        <div
            class="flex items-center space-x-2 bg-slate-100 px-3 py-1 rounded-full"
        >
            <svg
                class="w-5 h-5 text-slate-600"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
            >
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"
                ></path>
            </svg>
            <span class="font-mono font-bold text-slate-700"
                >{Math.ceil(timeRemaining)}s</span
            >
        </div>
    </div>

    <div
        class="absolute bottom-0 left-0 h-2 bg-linear-to-r from-blue-400 to-blue-600 rounded-full transition-all duration-100 ease-out"
        style="width: {progress}%;"
    ></div>
</div>

{#if !isLoading}
    <!-- content -->
    <div class="w-full mt-12 mb-6 px-4" in:fly={{ y: -20, duration: 500 }}>
        <div
            class="p-6 text-xl font-medium text-slate-800 bg-white rounded-2xl shadow-sm border border-slate-200 font-inter"
        >
            {quizies[activeQuestion].question}
        </div>
    </div>

    <!-- options -->
    <div
        class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8 px-4"
        in:fly={{ y: 10, delay: 100, duration: 500 }}
    >
        {#each quizies[activeQuestion].options as opt, index}
            <button
                onclick={() => (answers = opt)}
                class="group relative p-6 h-32 border-2 rounded-2xl transition-all duration-300 font-inter text-lg font-medium
                {opt === answers
                    ? 'border-blue-400 bg-blue-50 shadow-md transform'
                    : 'border-slate-300 bg-white hover:border-blue-300 hover:bg-blue-50 hover:shadow-md hover:transform hover:scale-102'}"
                style="animation-delay: {index * 50}ms"
            >
                <div class="flex items-center justify-center h-full">
                    <span class="text-center">{opt}</span>
                </div>
                {#if opt === answers}
                    <div class="absolute top-3 right-3">
                        <div
                            class="w-6 h-6 bg-blue-500 rounded-full flex items-center justify-center"
                        >
                            <svg
                                class="w-4 h-4 text-white"
                                fill="currentColor"
                                viewBox="0 0 20 20"
                            >
                                <path
                                    fill-rule="evenodd"
                                    d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                                    clip-rule="evenodd"
                                ></path>
                            </svg>
                        </div>
                    </div>
                {/if}
            </button>
        {/each}
    </div>

    <!-- button action -->
    <div
        class="flex justify-end mt-8 px-4"
        in:fly={{ y: 10, delay: 200, duration: 500 }}
    >
        <button
            onclick={handleNext}
            disabled={!answers}
            class="px-8 py-3 bg-blue-500 text-white font-bold rounded-2xl shadow-lg hover:bg-blue-600 hover:shadow-xl transform transition-all duration-300 hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed disabled:transform-none font-outfit"
        >
            {activeQuestion >= 9 ? "Finish Quiz" : "Next Question"}
        </button>
    </div>
{/if}

{#if showResult}
    <div
        class="fixed inset-0 bg-white bg-opacity-60 flex items-center justify-center z-50 backdrop-blur-sm"
        in:fade={{ duration: 300 }}
    >
        <div
            class="bg-white rounded-3xl p-8 max-w-2xl w-full mx-4 shadow-2xl transform transition-all"
            in:scale={{ duration: 300 }}
        >
            <div class="flex justify-between items-center mb-8">
                <h2 class="text-3xl font-bold text-slate-800 font-outfit">
                    Quiz Results
                </h2>
                <button
                    onclick={() => (showResult = false)}
                    class="text-slate-500 hover:text-slate-700 text-3xl font-bold transition-colors"
                >
                    ×
                </button>
            </div>

            <div class="text-center mb-8">
                <div class="mb-6">
                    <div
                        class="inline-flex items-center justify-center w-32 h-32 rounded-full bg-linear-to-br from-blue-400 to-blue-600 text-white text-5xl font-bold font-outfit shadow-lg"
                    >
                        {results.correct}
                    </div>
                </div>
                <h3 class="text-2xl font-bold text-slate-800 mb-2 font-outfit">
                    {results.correct >= 7
                        ? "Great Job!"
                        : results.correct >= 4
                          ? "Good Effort!"
                          : "Keep Practicing!"}
                </h3>
                <p class="text-lg text-slate-600 font-inter">
                    You got {results.correct} out of {results.correct +
                        results.incorrect} questions correct
                </p>
            </div>

            <div class="flex justify-center space-x-4">
                <button
                    onclick={() => goto("/")}
                    class="px-6 py-3 bg-slate-200 text-slate-800 font-bold rounded-2xl hover:bg-slate-300 transition-colors font-outfit"
                >
                    Back to Home
                </button>
                <button
                    onclick={() => {
                        showResult = false;
                        goto("/");
                    }}
                    class="px-6 py-3 bg-blue-500 text-white font-bold rounded-2xl hover:bg-blue-600 transition-colors font-outfit"
                >
                    Try Another Quiz
                </button>
            </div>
        </div>
    </div>
{/if}

{#if showScoreToast}
    <div
        class="fixed top-20 right-6 z-50"
        in:fly={{ x: 50, duration: 300 }}
        out:fly={{ x: 50, duration: 300 }}
    >
        <div
            class="bg-white rounded-2xl shadow-xl p-4 flex items-center space-x-3 border-2 {lastAnswerCorrect
                ? 'border-green-400'
                : 'border-red-400'}"
        >
            <div class="shrink-0">
                {#if lastAnswerCorrect}
                    <div
                        class="w-10 h-10 bg-green-100 rounded-full flex items-center justify-center"
                    >
                        <svg
                            class="w-6 h-6 text-green-600"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M5 13l4 4L19 7"
                            ></path>
                        </svg>
                    </div>
                {:else}
                    <div
                        class="w-10 h-10 bg-red-100 rounded-full flex items-center justify-center"
                    >
                        <svg
                            class="w-6 h-6 text-red-600"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M6 18L18 6M6 6l12 12"
                            ></path>
                        </svg>
                    </div>
                {/if}
            </div>
            <div>
                <p class="font-bold text-slate-800 font-inter">
                    {lastAnswerCorrect ? "Correct!" : "Incorrect"}
                </p>
                <p class="text-sm text-slate-600 font-inter">
                    {lastAnswerCorrect
                        ? "Great job!"
                        : "Better luck next time!"}
                </p>
            </div>
        </div>
    </div>
{/if}

<style>
    @keyframes loading-line {
        0% {
            left: 0%;
        }
        100% {
            left: 100%;
        }
    }
</style>
