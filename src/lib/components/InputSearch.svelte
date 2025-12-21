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
		class="w-full h-12 pl-12 pr-4 text-slate-700 bg-white border-2 border-slate-300 rounded-2xl focus:outline-none focus:border-blue-400 focus:ring-4 focus:ring-blue-100 transition-all duration-300 font-inter text-lg shadow-sm hover:shadow-md"
	/>
	<div class="absolute inset-y-0 left-0 flex items-center pl-4">
		<Search
			class="w-6 h-6 text-slate-400 cursor-pointer hover:text-blue-500 transition-colors"
			onclick={handleSearchClick}
		/>
	</div>
</div>
