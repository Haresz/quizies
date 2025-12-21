<script>
    import { onMount } from "svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { goto } from "$app/navigation";

    let quizies = $state([]);
    let isLoading = $state(true);
    let progress = $state(0);
    let interval = $state();
    let activeQuestion = $state(0);
    let answers = $state("");

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
            };
        });

        isLoading = false;
    }

    function handleNext() {
        if (activeQuestion > 9) return;
        quizies[activeQuestion].answer = answers;
        answers = "";

        activeQuestion += 1;
    }

    onMount(() => {
        isLoading = true;
        interval = setInterval(() => {
            if (progress < 100) {
                progress += 3; // speed control
            } else {
                clearInterval(interval);
            }
        }, 300);

        if ($globalQuiz.length > 0) {
            handleComposeQuiz();
        } else {
            goto("/");
        }

        return () => clearInterval(interval);
    });
</script>

<div class="flex justify-between border-b border-gray-300 pb-4 relative">
    <h2>Quizzes categories</h2>
    <p>{activeQuestion + 1}/10</p>

    <div
        class="absolute bottom-0 left-0 h-0.5 bg-blue-500 transition-all duration-75"
        style="width: {progress}%;"
    ></div>
</div>

{#if !isLoading}
    <!-- content -->
    <div class="w-full my-20 mx-auto">
        <div class="p-4 text-lg">
            {quizies[activeQuestion].question}
        </div>
    </div>

    <!-- options -->
    <div class="flex gap-4">
        {#each quizies[activeQuestion].options as opt}
            <button
                onclick={() => (answers = opt)}
                class="cursor-pointer flex justify-center items-center flex-1 h-60 border {opt ===
                answers
                    ? ' border-blue-400 bg-blue-100/50 hover:bg-blue-100'
                    : 'border-stone-400 hover:bg-gray-50'}  rounded-md transition-colors"
            >
                <h2>{opt}</h2>
            </button>
        {/each}
    </div>

    <!-- button action -->
    <div class="flex justify-end mt-32">
        <button
            onclick={handleNext}
            class=" cursor-pointer border border-stone-400 rounded-md px-8 py-2"
        >
            Next
        </button>
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
