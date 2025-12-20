<script>
    import { createEventDispatcher } from "svelte";
    import { setContext, onMount } from "svelte";

    // Props
    let {
        options = [],
        value = $bindable(),
        placeholder = "Select an option",
        disabled = false,
        required = false,
        multiple = false,
        searchable = false,
        clearable = false,
        maxHeight = "200px",
        class: className = "",
        label = "",
        description = "",
        error = "",
        size = "md", // sm, md, lg
        variant = "default", // default, outlined, filled
        prefix = "",
    } = $props();

    // State
    let isOpen = $state(false);
    let searchValue = $state("");
    let filteredOptions = $state([]);
    let highlightedIndex = $state(-1);
    let selectElement = $state();

    // Event dispatcher
    const dispatch = createEventDispatcher();

    // Size classes
    const sizeClasses = {
        sm: "text-sm px-2 py-1",
        md: "text-base px-3 py-2",
        lg: "text-lg px-4 py-3",
    };

    // Variant classes
    const variantClasses = {
        default: "border border-gray-300 bg-white focus:border-blue-500",
        outlined:
            "border-1 border-gray-300 bg-transparent focus:border-blue-500",
        filled: "border-0 bg-gray-100 focus:bg-white",
    };

    // Initialize filtered options
    $effect(() => {
        filteredOptions = options.filter((option) => {
            if (searchValue === "") return true;

            const label = option.label?.toString().toLowerCase() || "";
            const value = option.value?.toString().toLowerCase() || "";
            const search = searchValue.toLowerCase();

            return label.includes(search) || value.includes(search);
        });
    });

    // Toggle dropdown
    function toggleDropdown() {
        if (disabled) return;
        isOpen = !isOpen;
        if (isOpen) {
            searchValue = "";
            highlightedIndex = -1;
        }
    }

    // Select option
    function selectOption(option) {
        if (multiple) {
            if (value.includes(option.value)) {
                value = value.filter((v) => v !== option.value);
            } else {
                value = [...value, option.value];
            }
        } else {
            value = option.value;
            isOpen = false;
        }

        dispatch("change", { value, option });
    }

    // Clear selection
    function clearSelection() {
        value = multiple ? [] : "";
        dispatch("change", { value });
    }

    // Handle keyboard navigation
    function handleKeydown(event) {
        if (!isOpen) {
            if (event.key === "Enter" || event.key === " ") {
                event.preventDefault();
                toggleDropdown();
            }
            return;
        }

        switch (event.key) {
            case "ArrowDown":
                event.preventDefault();
                highlightedIndex = Math.min(
                    highlightedIndex + 1,
                    filteredOptions.length - 1,
                );
                break;
            case "ArrowUp":
                event.preventDefault();
                highlightedIndex = Math.max(highlightedIndex - 1, 0);
                break;
            case "Enter":
                event.preventDefault();
                if (
                    highlightedIndex >= 0 &&
                    filteredOptions[highlightedIndex]
                ) {
                    selectOption(filteredOptions[highlightedIndex]);
                }
                break;
            case "Escape":
                isOpen = false;
                break;
        }
    }

    // Get display text
    function getDisplayText() {
        if (multiple) {
            if (value.length === 0) return placeholder;
            if (value.length === 1) {
                const option = options.find((opt) => opt.value === value[0]);
                return option?.label || value[0];
            }
            return `${value.length} items selected`;
        } else {
            const option = options.find((opt) => opt.value === value);
            return option?.label || placeholder;
        }
    }

    // Check if option is selected
    function isSelected(optionValue) {
        return multiple ? value.includes(optionValue) : value === optionValue;
    }

    onMount(() => {
        const handleClickOutside = (event) => {
            if (selectElement && !selectElement.contains(event.target)) {
                isOpen = false;
            }
        };

        document.addEventListener("click", handleClickOutside);
        return () => document.removeEventListener("click", handleClickOutside);
    });
</script>

<div bind:this={selectElement} class="w-full {className}">
    {#if label}
        <!-- svelte-ignore a11y_label_has_associated_control -->
        <label class="block text-sm font-medium text-gray-700 mb-1">
            {label}
            {#if required}
                <span class="text-red-500">*</span>
            {/if}
        </label>
    {/if}

    <div class="relative">
        <!-- Trigger -->
        <!-- svelte-ignore a11y_role_has_required_aria_props -->
        <div
            class="flex items-center justify-between w-full {sizeClasses[
                size
            ]} {variantClasses[
                variant
            ]} rounded-md shadow-sm focus:shadow-blue-300 cursor-pointer focus:outline-none {disabled
                ? 'opacity-50 cursor-not-allowed'
                : ''}"
            class:border-red-500={error}
            onclick={toggleDropdown}
            onkeydown={handleKeydown}
            role="combobox"
            aria-expanded={isOpen}
            aria-haspopup="listbox"
            tabindex={disabled ? -1 : 0}
        >
            {#if prefix}
                <span
                    class="max-h-4 max-w-4 mr-2 text-black flex items-center justify-center"
                >
                    <!-- svelte-ignore svelte_component_deprecated -->
                    <svelte:component this={prefix} />
                </span>
            {/if}

            <span class="flex-1 truncate">
                {getDisplayText()}
            </span>

            <div class="flex items-center space-x-2">
                {#if clearable && value && (multiple ? value.length > 0 : true)}
                    <!-- svelte-ignore a11y_consider_explicit_label -->
                    <button
                        type="button"
                        class="text-gray-400 hover:text-gray-600 focus:outline-none"
                        onclick={(e) => {
                            e.stopPropagation();
                            clearSelection();
                        }}
                    >
                        <svg
                            class="w-4 h-4"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M6 18L18 6M6 6l12 12"
                            ></path>
                        </svg>
                    </button>
                {/if}

                <svg
                    class="w-4 h-4 text-gray-400 transition-transform {isOpen
                        ? 'rotate-180'
                        : ''}"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                >
                    <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M19 9l-7 7-7-7"
                    ></path>
                </svg>
            </div>
        </div>

        <!-- Dropdown -->
        {#if isOpen}
            <div
                class="absolute z-10 w-full mt-1 bg-white border border-gray-300 rounded-md shadow-lg"
            >
                {#if searchable}
                    <div class="p-2 border-b border-gray-200">
                        <input
                            type="text"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                            placeholder="Search..."
                            bind:value={searchValue}
                            onclick={(e) => e.stopPropagation()}
                            onkeydown={(e) => e.stopPropagation()}
                        />
                    </div>
                {/if}

                <div
                    class="overflow-auto"
                    style="max-height: {maxHeight}"
                    role="listbox"
                >
                    {#if filteredOptions.length === 0}
                        <div class="px-3 py-2 text-gray-500 text-center">
                            No options available
                        </div>
                    {:else}
                        {#each filteredOptions as option, index}
                            <!-- svelte-ignore a11y_interactive_supports_focus -->
                            <!-- svelte-ignore a11y_click_events_have_key_events -->
                            <div
                                class="px-3 py-2 cursor-pointer hover:bg-gray-100 flex items-center {highlightedIndex ===
                                index
                                    ? 'bg-gray-100'
                                    : ''} {isSelected(option.value)
                                    ? 'bg-blue-50 text-blue-600'
                                    : ''}"
                                class:hover:bg-blue-50={isSelected(
                                    option.value,
                                )}
                                onclick={() => selectOption(option)}
                                onmouseenter={() => (highlightedIndex = index)}
                                role="option"
                                aria-selected={isSelected(option.value)}
                            >
                                {#if multiple}
                                    <input
                                        type="checkbox"
                                        checked={isSelected(option.value)}
                                        class="mr-2"
                                        readonly
                                    />
                                {/if}

                                {#if option?.icon}
                                    <span
                                        class="max-h-4 max-w-4 mr-2 text-black flex items-center justify-center"
                                    >
                                        <!-- svelte-ignore svelte_component_deprecated -->
                                        <svelte:component this={option.icon} />
                                    </span>
                                {/if}

                                <span class="flex-1 truncate">
                                    {option.label || option.value}
                                </span>

                                {#if option.description}
                                    <span class="text-xs text-gray-500 ml-2">
                                        {option.description}
                                    </span>
                                {/if}
                            </div>
                        {/each}
                    {/if}
                </div>
            </div>
        {/if}
    </div>

    {#if description}
        <p class="mt-1 text-sm text-gray-500">
            {description}
        </p>
    {/if}

    {#if error}
        <p class="mt-1 text-sm text-red-600">
            {error}
        </p>
    {/if}
</div>

<style>
    /* Custom styles for better accessibility */
    [role="option"]:focus {
        outline: 2px solid #3b82f6;
        outline-offset: -2px;
    }
</style>
