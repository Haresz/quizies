<script>
    let {
        value = $bindable(""),
        placeholder = "Search...",
        class: additionalClasses = "",
        primaryColor = "#FF6B00",
        shadowColor = "#CC5500",
        textColor = "#4A4A4A",
        backgroundColor = "#FFFFFF",
        oninput,
        onfocus,
        onblur,
    } = $props();

    let isFocused = $state(false);

    function handleFocus(e) {
        isFocused = true;
        if (onfocus) onfocus(e);
    }

    function handleBlur(e) {
        isFocused = false;
        if (onblur) onblur(e);
    }

    function handleInput(e) {
        value = e.target.value;
        if (oninput) oninput(e);
    }

    // Calculate dynamic styles based on focus state
    const translateY = $derived(isFocused ? "2px" : "0");
    const insetShadow = $derived(
        isFocused
            ? "inset 0 -1px 0 rgba(204, 85, 0, 0.3), inset 0 1px 2px rgba(0, 0, 0, 0.1)"
            : "inset 0 -4px 0 rgba(204, 85, 0, 0.3), inset 0 2px 4px rgba(0, 0, 0, 0.1)",
    );
</script>

<div class="relative w-full {additionalClasses}">
    <input
        type="text"
        {placeholder}
        bind:value
        oninput={handleInput}
        onfocus={handleFocus}
        onblur={handleBlur}
        class="w-full h-14 pl-14 pr-6 text-xl font-bold rounded-full focus:outline-none transition-all duration-150"
        style="
            font-family: 'Nunito', sans-serif;
            background-color: {backgroundColor};
            color: {textColor};
            border: 3px solid {primaryColor};
            transform: translateY({translateY});
            box-shadow: {insetShadow};
        "
    />
    <div class="absolute inset-y-0 left-0 flex items-center pl-5">
        <svg
            class="w-6 h-6"
            style="color: {primaryColor};"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
        >
            <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="3"
                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
            ></path>
        </svg>
    </div>
</div>
