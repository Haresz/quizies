<script>
    import InputSearch from "$lib/components/InputSearch.svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { categoriesData, levelsData } from "$lib/config/content.js";
    import { goto } from "$app/navigation";
    import { fade, fly, scale } from "svelte/transition";

    let value = $state("");
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

<!-- wording for quiz -->
<div class="text-center mb-12" in:fly={{ y: -20, duration: 500 }}>
    <h1 class="text-5xl font-bold text-slate-800 mb-4 font-outfit">
        Discover Amazing Quizzes
    </h1>
    <p class="text-xl text-slate-600 max-w-2xl mx-auto font-inter">
        Test your knowledge with our wide variety of quiz categories
    </p>
</div>

<div
    class="flex justify-center mb-12 max-w-2xl mx-auto"
    in:fly={{ y: -10, delay: 200, duration: 500 }}
>
    <!-- for input search -->
    <InputSearch
        {value}
        placeholder="Search for categories..."
        onSearch={handleSearch}
    />
</div>

<!-- items categoryy quiz -->
<div
    class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6 mb-12"
    in:fly={{ y: 10, delay: 300, duration: 500 }}
>
    {#each filteredCategories as item, index}
        <button
            onclick={() => handleCategorySelect(item)}
            class="group relative p-6 rounded-3xl border-2 {item.borderColor} {item.color} hover:shadow-lg transform transition-all duration-300 hover:scale-105 hover:-translate-y-1"
            style="animation-delay: {index * 50}ms"
        >
            <div class="flex flex-col items-center justify-center space-y-3">
                {@html item.icon}
                <h3
                    class="text-sm font-semibold text-slate-800 text-center font-inter"
                >
                    {item.name}
                </h3>
            </div>
        </button>
    {/each}
</div>

{#if alreadyChoseCategorry}
    <div
        class="fixed inset-0 bg-white bg-opacity-60 flex items-center justify-center z-50 backdrop-blur-sm"
        in:fade={{ duration: 300 }}
        out:fade={{ duration: 300 }}
    >
        <div
            class="bg-white rounded-3xl p-8 max-w-2xl w-full mx-4 shadow-2xl transform transition-all"
            in:scale={{ duration: 300 }}
            out:scale={{ duration: 300 }}
        >
            <div class="flex justify-between items-center mb-8">
                <h2 class="text-3xl font-bold text-slate-800 font-outfit">
                    Select Difficulty Level
                </h2>
                <button
                    onclick={closeLevelSelection}
                    class="text-slate-500 hover:text-slate-700 text-3xl font-bold transition-colors"
                >
                    ×
                </button>
            </div>

            <p class="text-lg text-slate-600 mb-8 font-inter">
                Choose a difficulty level for <span
                    class="font-semibold text-slate-800"
                    >{selectedCategory?.name}</span
                >
            </p>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                {#each levelsData as level, index}
                    <button
                        onclick={() => handleLevelSelect(level)}
                        disabled={isLoading}
                        class="group relative p-6 rounded-2xl border-2 {level.borderColor} {level.color} {level.hoverColor} transition-all duration-300 {isLoading
                            ? 'opacity-50 cursor-not-allowed'
                            : 'hover:shadow-lg hover:scale-105'}"
                        style="animation-delay: {index * 100}ms"
                    >
                        <div
                            class="flex flex-col items-center justify-center space-y-3"
                        >
                            {@html level.icon}
                            <h3
                                class="text-xl font-bold text-slate-800 font-outfit"
                            >
                                {level.name}
                            </h3>
                            <p
                                class="text-sm text-slate-600 text-center font-inter"
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
            class="fixed inset-0 bg-white bg-opacity-80 flex items-center justify-center z-50 backdrop-blur-sm"
            in:fade={{ duration: 300 }}
        >
            <div
                class="flex flex-col items-center space-y-4 bg-white rounded-3xl p-8 shadow-2xl"
                in:scale={{ duration: 300 }}
            >
                <div class="relative">
                    <div
                        class="animate-spin rounded-full h-16 w-16 border-4 border-slate-200"
                    ></div>
                    <div
                        class="animate-spin rounded-full h-16 w-16 border-4 border-blue-500 border-t-transparent absolute top-0 left-0"
                    ></div>
                </div>
                <p class="text-xl font-semibold text-slate-700 font-inter">
                    Loading quiz questions...
                </p>
            </div>
        </div>
    {/if}
{/if}
