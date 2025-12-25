<script>
    import SearchInput from "$lib/components/SearchInput.svelte";
    import Button3D from "$lib/components/Buttton3D.svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { categoriesData, levelsData } from "$lib/config/content.js";
    import { goto } from "$app/navigation";
    import { fade, fly, scale } from "svelte/transition";

    let searchValue = $state("");
    let alreadyChoseCategorry = $state(false);
    let selectedCategory = $state(null);
    let selectedLevel = $state(null);
    let quizie = $state([]);
    let isLoading = $state(false);
    let filteredCategories = $state(categoriesData);

    function handleSearch(searchTerm) {
        if (!searchTerm.trim()) {
            filteredCategories = categoriesData;
            return;
        }

        const term = searchTerm.toLowerCase();
        filteredCategories = categoriesData.filter((category) =>
            category.name.toLowerCase().includes(term),
        );
    }

    function handleCategorySelect(category) {
        selectedCategory = category;
        alreadyChoseCategorry = true;
    }

    async function handleLevelSelect(level) {
        selectedLevel = level;

        await handleGetQuiz();
        alreadyChoseCategorry = false;
    }

    function closeLevelSelection() {
        alreadyChoseCategorry = false;
    }

    async function handleGetQuiz() {
        if (!selectedCategory || !selectedLevel) return;

        isLoading = true;

        try {
            const req = await fetch(
                `https://opentdb.com/api.php?amount=10&category=${selectedCategory.id}&difficulty=${selectedLevel.id}`,
            );

            const res = await req.json();

            quizie = res.results;
            globalQuiz.set(quizie);

            if ($globalQuiz.length >= 0) {
                goto("/quiz");
            }
        } catch (error) {
            console.log(error);
        } finally {
            isLoading = false;
        }
    }
</script>

<svelte:head>
    <title>Quezyy - Choose Quiz Category</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
        href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:wght@400;600;700&display=swap"
        rel="stylesheet"
    />
</svelte:head>

<div class="min-h-screen w-full bg-[#FAF9E6] px-4 py-8">
    <!-- wording for quiz -->
    <div class="text-center mb-12" in:fly={{ y: -20, duration: 500 }}>
        <h1
            class="text-5xl font-bold mb-4"
            style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 2px 2px 0px rgba(0,0,0,0.2);"
        >
            Discover Amazing Quizzes
        </h1>
        <p
            class="text-xl max-w-2xl mx-auto"
            style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
        >
            Test your knowledge with our wide variety of quiz categories
        </p>
    </div>

    <div
        class="flex justify-center mb-12 max-w-2xl mx-auto"
        in:fly={{ y: -10, delay: 200, duration: 500 }}
    >
        <!-- for input search -->
        <SearchInput
            bind:value={searchValue}
            placeholder="Search for categories..."
            oninput={(e) => handleSearch(e.target.value)}
            class="w-full"
        />
    </div>

    <!-- items categoryy quiz -->
    <div
        class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-6 mb-12"
        in:fly={{ y: 10, delay: 300, duration: 500 }}
    >
        {#each filteredCategories as item, index}
            <button
                onclick={() => handleCategorySelect(item)}
                class=" cursor-pointer group relative w-full p-6 rounded-3xl bg-white border-2 border-slate-200 border-b-[6px] border-b-slate-200 transition-all duration-200 ease-out transform hover:-translate-y-2 hover:border-orange-300 hover:border-b-orange-400 hover:shadow-lg hover:shadow-orange-100 active:translate-y-1 active:border-b-0 active:shadow-none overflow-hidden"
                style="animation-delay: {index * 50}ms"
            >
                <!-- <img
                    src={item.img || "/fox.png"}
                    alt="Fox decoration"
                    class="absolute -bottom-4 -right-4 w-24 h-24 object-contain opacity-30 grayscale contrast-50 pointer-events-none group-hover:opacity-50 group-hover:grayscale-0 transition-all duration-300"
                /> -->

                <div
                    class="relative z-10 flex flex-col items-center justify-center space-y-4"
                >
                    <!-- <div
                        class="flex items-center justify-center w-16 h-16 rounded-full bg-orange-50 text-[#FF6B00] group-hover:bg-[#FF6B00] group-hover:text-white transition-colors duration-200"
                    >
                        <div class="w-8 h-8">{@html item.icon}</div>
                    </div> -->
                    <div class="flex items-center justify-center">
                        <img
                            src={item.img || "/fox.png"}
                            alt="Fox decoration"
                            class=" w-24 h-24 object-contain opacity-30 grayscale contrast-80 pointer-events-none group-hover:opacity-90 group-hover:grayscale-0 transition-all duration-300"
                        />
                    </div>
                    <h3
                        class="text-base font-bold text-center text-slate-600 group-hover:text-[#FF6B00] transition-colors"
                        style="font-family: 'Nunito', sans-serif;"
                    >
                        {item.name}
                    </h3>
                </div>
            </button>
        {/each}
    </div>

    {#if alreadyChoseCategorry}
        <div
            class="fixed inset-0 bg-[#FAF9E6] bg-opacity-90 flex items-center justify-center z-50 backdrop-blur-sm"
            in:fade={{ duration: 300 }}
            out:fade={{ duration: 300 }}
        >
            <div
                class="bg-white rounded-3xl p-8 max-w-2xl w-full mx-4 shadow-2xl transform transition-all border-4 border-[#CC5500]"
                in:scale={{ duration: 300 }}
                out:scale={{ duration: 300 }}
            >
                <div class="flex justify-between items-center mb-8">
                    <h2
                        class="text-3xl font-bold"
                        style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 1px 1px 0px rgba(0,0,0,0.1);"
                    >
                        Select Difficulty Level
                    </h2>
                    <button
                        onclick={closeLevelSelection}
                        class="text-4xl font-bold transition-colors hover:scale-110 transform"
                        style="font-family: 'Fredoka One', cursive; color: #FF6B00;"
                    >
                        ×
                    </button>
                </div>

                <p
                    class="text-lg mb-8"
                    style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
                >
                    Choose a difficulty level for <span
                        class="font-bold"
                        style="color: #FF6B00;">{selectedCategory?.name}</span
                    >
                </p>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    {#each levelsData as level, index}
                        <button
                            onclick={() => handleLevelSelect(level)}
                            disabled={isLoading}
                            class="group relative p-6 rounded-2xl border-4 border-[#CC5500] bg-white hover:shadow-xl transition-all duration-300 {isLoading
                                ? 'opacity-50 cursor-not-allowed'
                                : 'hover:scale-105 hover:-translate-y-1'}"
                            style="animation-delay: {index * 100}ms"
                        >
                            <div
                                class="flex flex-col items-center justify-center space-y-3"
                            >
                                <div class="text-[#FF6B00]">
                                    {@html level.icon}
                                </div>
                                <h3
                                    class="text-xl font-bold"
                                    style="font-family: 'Fredoka One', cursive; color: #4A4A4A;"
                                >
                                    {level.name}
                                </h3>
                                <p
                                    class="text-sm text-center"
                                    style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
                                >
                                    {level.description}
                                </p>
                            </div>
                        </button>
                    {/each}
                </div>
            </div>
        </div>

        {#if isLoading}
            <div
                class="fixed inset-0 bg-[#FAF9E6] bg-opacity-90 flex items-center justify-center z-50 backdrop-blur-sm"
                in:fade={{ duration: 300 }}
            >
                <div
                    class="flex flex-col items-center space-y-6 bg-white rounded-3xl p-8 shadow-2xl border-4 border-[#CC5500]"
                    in:scale={{ duration: 300 }}
                >
                    <div class="relative">
                        <div
                            class="animate-spin rounded-full h-16 w-16 border-4 border-[#FF6B00]/30"
                        ></div>
                        <div
                            class="animate-spin rounded-full h-16 w-16 border-4 border-[#FF6B00] border-t-transparent absolute top-0 left-0"
                        ></div>
                    </div>
                    <p
                        class="text-xl font-bold"
                        style="font-family: 'Nunito', sans-serif; color: #4A4A4A;"
                    >
                        Loading quiz questions...
                    </p>
                </div>
            </div>
        {/if}
    {/if}
</div>

<style>
    :global(body) {
        background-color: #faf9e6;
        margin: 0;
        padding: 0;
        font-family: "Nunito", sans-serif;
        min-height: 100vh;
        width: 100%;
    }

    :global(html) {
        min-height: 100vh;
        width: 100%;
        margin: 0;
        padding: 0;
    }

    /* Responsive adjustments */
    @media (max-width: 640px) {
        :global(h1) {
            font-size: 2.5rem !important;
        }

        :global(.text-xl) {
            font-size: 1rem !important;
        }
    }
</style>
