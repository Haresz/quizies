<script>
    import { onMount } from "svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { goto } from "$app/navigation";
    import { fly, scale, fade } from "svelte/transition";
    import Button3D from "$lib/components/Buttton3D.svelte";

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

<div class="flex justify-between items-center pb-6 relative mt-8 mx-4">
    <h2
        class="text-2xl font-bold"
        style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 2px 2px 0px rgba(0,0,0,0.2);"
    >
        Quiz Challenge
    </h2>
    <div class="flex items-center space-x-4">
        <div
            class="text-lg font-semibold"
            style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
        >
            Question {activeQuestion + 1}/10
        </div>
        <div
            class="flex items-center space-x-2 px-3 py-1 rounded-full"
            style="background-color: #FFF8E1;"
        >
            <svg
                class="w-5 h-5"
                style="color: #FF6B00;"
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
            <span class="font-mono font-bold" style="color: #FF6B00; "
                >{Math.ceil(timeRemaining)}s</span
            >
        </div>
    </div>
</div>

<div class="relative rounded-full h-2 my-4 mx-4 bg-[#E0E0E0]">
    <div
        class="absolute bottom-0 left-0 h-2 rounded-full transition-all duration-100 ease-out ]"
        style="width: {progress}%; background: linear-gradient(to right, #FF6B00, #CC5500); "
    ></div>
</div>

{#if !isLoading}
    <!-- content -->
    <div class="w-full mt-12 mb-6 px-4" in:fly={{ y: -20, duration: 500 }}>
        <div
            class="p-6 text-xl font-medium font-inter"
            style="background-color: #FFFFFF; color: #4A4A4A; border-radius: 16px; border: 4px solid #FF6B00; box-shadow: 0 4px 0 #CC5500; font-family: 'Nunito', sans-serif;"
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
                class="group relative p-6 h-32 transition-all rounded-3xl duration-300 font-inter text-lg font-medium"
                style="animation-delay: {index *
                    50}ms; font-family: 'Nunito', sans-serif; background-color: {opt ===
                answers
                    ? '#FFF8E1'
                    : '#FFFFFF'}; border: 4px solid {opt === answers
                    ? '#FF6B00'
                    : '#E0E0E0'}; box-shadow: 0 6px 0 {opt === answers
                    ? '#CC5500'
                    : '#CCCCCC'}; transform: {opt === answers
                    ? 'translateY(-2px)'
                    : 'translateY(0)'};"
            >
                <div class="flex items-center justify-center h-full">
                    <span class="text-center">{opt}</span>
                </div>
                {#if opt === answers}
                    <div class="absolute top-3 right-3">
                        <div
                            class="w-6 h-6 rounded-full flex items-center justify-center"
                            style="background-color: #FF6B00; box-shadow: 0 3px 0 #CC5500;"
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
        <Button3D
            text={activeQuestion >= 9 ? "Finish Quiz" : "Next Question"}
            bgColor="#FF6B00"
            shadowColor="#CC5500"
            textColor="#FFFFFF"
            size="md"
            outlineWidth={6}
            fullWidth={false}
            disabled={!answers}
            on:click={handleNext}
        />
    </div>
{/if}

{#if showResult}
    <div
        class="fixed inset-0 bg-white bg-opacity-60 flex items-center justify-center z-50 backdrop-blur-sm"
        in:fade={{ duration: 300 }}
    >
        <div
            class="p-8 max-w-2xl w-full mx-4 transform transition-all"
            style="background-color: #FFFFFF; border-radius: 16px; border: 4px solid #FF6B00; box-shadow: 0 12px 0 #CC5500; font-family: 'Nunito', sans-serif;"
            in:scale={{ duration: 300 }}
        >
            <div class="flex justify-between items-center mb-8">
                <h2
                    class="text-3xl font-bold"
                    style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 2px 2px 0px rgba(0,0,0,0.2);"
                >
                    Quiz Results
                </h2>
                <button
                    onclick={() => (showResult = false)}
                    class="text-3xl font-bold transition-colors"
                    style="color: #4A4A4A; font-family: 'Nunito', sans-serif;"
                >
                    ×
                </button>
            </div>

            <div class="text-center mb-8">
                <div class="mb-6">
                    <div
                        class="inline-flex items-center justify-center w-32 h-32 rounded-full text-white text-5xl font-bold shadow-lg"
                        style="background: linear-gradient(to bottom right, #FF6B00, #CC5500); box-shadow: 0 8px 0 #AA4400; font-family: 'Fredoka One', cursive;"
                    >
                        {results.correct}
                    </div>
                </div>
                <h3
                    class="text-2xl font-bold mb-2"
                    style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 2px 2px 0px rgba(0,0,0,0.2);"
                >
                    {results.correct >= 7
                        ? "Great Job!"
                        : results.correct >= 4
                          ? "Good Effort!"
                          : "Keep Practicing!"}
                </h3>
                <p
                    class="text-lg"
                    style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
                >
                    You got {results.correct} out of {results.correct +
                        results.incorrect} questions correct
                </p>
            </div>

            <div class="flex justify-center space-x-4">
                <Button3D
                    text="Back to Home"
                    bgColor="#E0E0E0"
                    shadowColor="#CCCCCC"
                    textColor="#4A4A4A"
                    size="md"
                    outlineWidth={6}
                    fullWidth={false}
                    on:click={() => goto("/")}
                />
                <Button3D
                    text="Try Another Quiz"
                    bgColor="#FF6B00"
                    shadowColor="#CC5500"
                    textColor="#FFFFFF"
                    size="md"
                    outlineWidth={6}
                    fullWidth={false}
                    on:click={() => {
                        showResult = false;
                        goto("/");
                    }}
                />
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
            class="p-4 flex items-center space-x-3"
            style="background-color: #FFFFFF; border-radius: 16px; box-shadow: 0 8px 0 rgba(0,0,0,0.1); font-family: 'Nunito', sans-serif; border: 4px solid {lastAnswerCorrect
                ? '#4CAF50'
                : '#F44336'};"
        >
            <div class="shrink-0">
                {#if lastAnswerCorrect}
                    <div
                        class="w-10 h-10 rounded-full flex items-center justify-center"
                        style="background-color: #E8F5E9;"
                    >
                        <svg
                            class="w-6 h-6"
                            style="color: #4CAF50;"
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
                        class="w-10 h-10 rounded-full flex items-center justify-center"
                        style="background-color: #FFEBEE;"
                    >
                        <svg
                            class="w-6 h-6"
                            style="color: #F44336;"
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
                <p
                    class="font-bold"
                    style="color: #4A4A4A; font-family: 'Nunito', sans-serif;"
                >
                    {lastAnswerCorrect ? "Correct!" : "Incorrect"}
                </p>
                <p
                    class="text-sm"
                    style="color: #4A4A4A; font-family: 'Nunito', sans-serif;"
                >
                    {lastAnswerCorrect
                        ? "Great job!"
                        : "Better luck next time!"}
                </p>
            </div>
        </div>
    </div>
{/if}

<style>
    :global(body) {
        background-color: #faf9e6;
        margin: 0;
        padding: 0;
        font-family: "Nunito", sans-serif;
        overflow-x: hidden;
    }

    :global(html) {
        background-color: #faf9e6;
        margin: 0;
        padding: 0;
    }

    @keyframes loading-line {
        0% {
            left: 0%;
        }
        100% {
            left: 100%;
        }
    }
</style>
