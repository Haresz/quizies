<script>
    import InputSearch from "$lib/components/InputSearch.svelte";

    let value = $state("");

    function handleSearch(searchTerm) {
        console.log("Searching for:", searchTerm);
        // Here you would implement the actual search logic
    }
</script>

<!-- wording for quiz -->
<h1 class="text-3xl font-bold text-gray-800 mb-2">Discover Amazing Quizzes</h1>
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
<div class="w-full flex flex-wrap gap-3">
    {#each Array(24) as item}
        <div class=" min-w-60 h-40 bg-red-200 rounded-md"></div>
    {/each}
</div>
