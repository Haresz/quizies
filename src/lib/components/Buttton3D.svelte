<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import { onDestroy } from "svelte";

    // Props with default values using Svelte 5 syntax
    let {
        text = "Button",
        bgColor = "#FF6B00",
        shadowColor = null, // Will be auto-generated if not provided
        textColor = "#FFFFFF",
        size = "md", // 'sm', 'md', 'lg'
        outlineWidth = 6, // Default 6px
        fullWidth = false,
        disabled = false,
    } = $props();

    // State for button press animation using Svelte 5 state
    let buttonPressed = $state(false);
    let timeoutId = $state(null);

    // Create event dispatcher
    const dispatch = createEventDispatcher();

    // Size configurations
    const sizeConfig = {
        sm: { padding: "py-2 px-4", fontSize: "text-base" },
        md: { padding: "py-4 px-6", fontSize: "text-xl" },
        lg: { padding: "py-5 px-8", fontSize: "text-2xl" },
    };

    // Function to generate a darker shade of a color
    function generateDarkerShade(color) {
        // Convert hex to RGB
        const hex = color.replace("#", "");
        const r = parseInt(hex.substring(0, 2), 16);
        const g = parseInt(hex.substring(2, 4), 16);
        const b = parseInt(hex.substring(4, 6), 16);

        // Darken by 20%
        const darkerR = Math.floor(r * 0.8);
        const darkerG = Math.floor(g * 0.8);
        const darkerB = Math.floor(b * 0.8);

        // Convert back to hex
        return `#${darkerR.toString(16).padStart(2, "0")}${darkerG.toString(16).padStart(2, "0")}${darkerB.toString(16).padStart(2, "0")}`;
    }

    // Handle button click with animation
    function handleClick() {
        if (disabled) return;

        buttonPressed = true;
        dispatch("click");

        // Clear any existing timeout
        if (timeoutId) {
            clearTimeout(timeoutId);
        }

        // Reset animation state after a short delay
        timeoutId = setTimeout(() => {
            buttonPressed = false;
            timeoutId = null;
        }, 150);
    }

    // Clean up timeout on component destruction
    onDestroy(() => {
        if (timeoutId) {
            clearTimeout(timeoutId);
        }
    });

    // Derived values using Svelte 5 syntax
    const computedShadowColor = $derived(
        shadowColor || generateDarkerShade(bgColor),
    );
    const currentSize = $derived(sizeConfig[size] || sizeConfig.md);
    const buttonClasses = $derived(
        [
            fullWidth ? "w-full" : "",
            currentSize.padding,
            currentSize.fontSize,
            disabled ? "cursor-not-allowed opacity-60" : "cursor-pointer",
            "font-bold",
            "rounded-xl",
            "transition-all",
            "duration-150",
            "transform",
            `border-b-[${outlineWidth}px]`,
            buttonPressed && !disabled ? "translate-y-1.5" : "",
        ]
            .filter(Boolean)
            .join(" "),
    );
</script>

<button
    class={buttonClasses}
    style="
        background-color: {disabled ? '#CCCCCC' : bgColor};
        color: {disabled ? '#999999' : textColor};
        border-color: {disabled ? '#999999' : computedShadowColor};
        font-family: 'Nunito', sans-serif;
        {buttonPressed && !disabled
        ? ''
        : `box-shadow: 0 ${outlineWidth}px 0 ${disabled ? '#999999' : computedShadowColor};`}
    "
    onclick={handleClick}
    {disabled}
>
    {text}
</button>
