<script>
    import { onMount } from "svelte";

    export let quizies = [];

    let isLoading = true;
    let progress = 0;
    let interval;

    onMount(() => {
        interval = setInterval(() => {
            if (progress < 100) {
                progress += 2; // speed control
            } else {
                clearInterval(interval);
                isLoading = false;
            }
        }, 30);

        return () => clearInterval(interval);
    });
</script>

<div class="flex justify-between border-b border-gray-300 pb-4 relative">
    <h2>Quizzes categories</h2>
    <p>9/10</p>

    {#if isLoading}
        <div
            class="absolute bottom-0 left-0 h-0.5 bg-blue-500 transition-all duration-75"
            style="width: {progress}%;"
        ></div>
    {/if}
</div>

<!-- content -->
<div class="w-full my-20 mx-auto">
    <div class="p-4 text-lg">
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Magnam commodi
        at doloribus alias natus nobis ut, eligendi, vero perspiciatis hic,
        nostrum ipsum placeat sequi.
    </div>
</div>

<!-- options -->
<div class="flex gap-4">
    {#each Array(3) as _}
        <button
            onclick={() => ""}
            class="cursor-pointer flex justify-center items-center flex-1 h-60 border border-stone-400 rounded-md hover:bg-gray-50 transition-colors"
        >
            <h2>Option</h2>
        </button>
    {/each}
</div>

<!-- button action -->
<div class="flex justify-end mt-32">
    <button
        class=" cursor-pointer border border-stone-400 rounded-md px-8 py-2"
    >
        Next
    </button>
</div>

<style>
    @keyframes loading-line {
        0% {
            left: -30%;
        }
        100% {
            left: 100%;
        }
    }
</style>
