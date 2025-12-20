<script lang="ts">
	import { Search } from "@lucide/svelte";

	let {
		placeholder = "Search...",
		value = "",
		onSearch = () => {},
	} = $props();

	function handleInput(event: Event) {
		const target = event.target as HTMLInputElement;
		value = target.value;
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === "Enter") {
			onSearch(value);
		}
	}

	function handleSearchClick() {
		onSearch(value);
	}
</script>

<div class="relative w-full">
	<input
		type="text"
		{placeholder}
		bind:value
		oninput={handleInput}
		onkeydown={handleKeydown}
		class="w-full h-10 pl-10 pr-4 text-gray-700 bg-white border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition-colors"
	/>
	<div class="absolute inset-y-0 left-0 flex items-center pl-3">
		<Search
			class="w-5 h-5 text-gray-400 cursor-pointer hover:text-gray-600 transition-colors"
			onclick={handleSearchClick}
		/>
	</div>
</div>
