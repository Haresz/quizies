<script>
    import InputSearch from "$lib/components/InputSearch.svelte";
    import { globalQuiz } from "$lib/stores/index.js";
    import { categoriesData, levelsData } from "$lib/config/content.js";
    import { goto } from "$app/navigation";

    let value = $state("");
    let alreadyChoseCategorry = $state(false);
    let selectedCategory = $state(null);
    let selectedLevel = $state(null);
    let quizie = $state([]);

    function handleSearch(searchTerm) {
        console.log("Searching for:", searchTerm);
    }

    function handleCategorySelect(category) {
        selectedCategory = category;
        alreadyChoseCategorry = true;
    }

    function handleLevelSelect(level) {
        selectedLevel = level;
        alreadyChoseCategorry = false;

        handleGetQuiz();
    }

    function closeLevelSelection() {
        alreadyChoseCategorry = false;
    }

    async function handleGetQuiz() {
        if (!selectedCategory || !selectedLevel) return;

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
        }
    }
</script>

<!-- wording for quiz -->
<h1 class="text-3xl font-bold text-gray-800 mt-8 mb-2">
    Discover Amazing Quizzes
</h1>
<p class="text-gray-600 mb-8">
    Test your knowledge with our wide variety of quiz categories
</p>

<div class="flex mt-10 mb-12 max-w-2xl">
    <!-- for input search -->
    <InputSearch
        {value}
        placeholder="Search for quizzes..."
        onSearch={handleSearch}
    />
</div>

<!-- items categoryy quiz -->
<div class="w-full flex md:justify-start justify-center flex-wrap gap-2">
    {#each categoriesData as item}
        <button
            onclick={() => handleCategorySelect(item)}
            class="cursor-pointer flex justify-center items-center sm:max-w-60 w-full h-40 border border-stone-400 rounded-md hover:bg-gray-50 transition-colors"
        >
            <h2>{item.name}</h2>
        </button>
    {/each}
</div>

{#if alreadyChoseCategorry}
    <div
        class="fixed inset-0 bg-white bg-opacity-50 flex items-center justify-center z-50"
    >
        <div class="bg-white rounded-lg p-6 max-w-2xl w-full -mt-24 mx-4">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-bold text-gray-800">
                    Select Difficulty Level
                </h2>
                <button
                    onclick={closeLevelSelection}
                    class="text-gray-500 hover:text-gray-700 text-2xl font-bold"
                >
                    ×
                </button>
            </div>

            <p class="text-gray-600 mb-6">
                Choose a difficulty level for {selectedCategory?.name}
            </p>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                {#each levelsData as level}
                    <button
                        onclick={() => handleLevelSelect(level)}
                        class="flex flex-col justify-center items-center p-6 border-2 border-gray-300 rounded-lg hover:border-blue-500 hover:bg-blue-50 transition-all"
                    >
                        <h3 class="text-lg font-semibold text-gray-800 mb-2">
                            {level.name}
                        </h3>
                        <p class="text-sm text-gray-600 text-center">
                            {level.description}
                        </p>
                    </button>
                {/each}
            </div>
        </div>
    </div>
{/if}
