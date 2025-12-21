<script>
    import { onMount } from "svelte";
    import Button3D from "$lib/components/Buttton3D.svelte";

    const handlePlayNow = () => {
        // Navigate to home page to select quiz category
        window.location.href = "/home";
    };
</script>

<svelte:head>
    <title>Quezyy - Quiz Game</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
        href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:wght@400;600;700&display=swap"
        rel="stylesheet"
    />
</svelte:head>

<div
    class="min-h-screen w-full md:-mt-20 flex flex-col justify-center gap-16 bg-[#FAF9E6] overflow-hidden"
>
    <!-- Main App Container -->
    <div class=" flex flex-col justify-between w-full md:mx-auto">
        <!-- Top Section with Logo and Button -->
        <div class="flex-1 flex flex-col items-center justify-center p-6">
            <!-- Logo and Title -->
            <div class="text-center mb-4">
                <h1
                    class="font-bold text-6xl mb-2"
                    style="font-family: 'Fredoka One', cursive; color: #FF6B00; text-shadow: 2px 2px 0px rgba(0,0,0,0.2);"
                >
                    Quezyy
                </h1>
                <p
                    class="text-[#4A4A4A] text-lg"
                    style="font-family: 'Nunito', sans-serif;"
                >
                    Join and play to get a wider knowledge insight
                </p>
            </div>
        </div>

        <!-- Bottom Section with Illustration -->
        <div
            class=" flex-col justify-center items-center relative max-w-xs mx-auto p-6"
        >
            <img
                src="/fox.png"
                style="margin-bottom: -50px;"
                class="max-h-80"
                alt="ilustration"
            />
            <!-- Play Now Button -->
            <div class="w-full max-w-xs">
                <Button3D
                    text="Play Now"
                    bgColor="#FF6B00"
                    shadowColor="#CC5500"
                    textColor="#FFFFFF"
                    size="md"
                    outlineWidth={6}
                    fullWidth={true}
                    on:click={handlePlayNow}
                />
            </div>
        </div>
    </div>
</div>

<style>
    :global(body) {
        background-color: #faf9e6;
        margin: 0;
        padding: 0;
        font-family: "Nunito", sans-serif;
        overflow: hidden;
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
            font-size: 3.5rem !important;
        }

        :global(.text-lg) {
            font-size: 1rem !important;
        }

        :global(button) {
            padding: 1rem 2rem !important;
            font-size: 1rem !important;
        }
    }
</style>
