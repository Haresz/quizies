<script>
    import { onMount } from "svelte";

    let buttonPressed = false;

    const handlePlayNow = () => {
        buttonPressed = true;
        setTimeout(() => {
            buttonPressed = false;
            // Navigate to home page to select quiz category
            // window.location.href = "/home";
        }, 200);
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

<div class="min-h-screen w-full flex flex-col bg-[#FAF9E6] overflow-hidden">
    <!-- Main App Container -->
    <div class="flex-1 flex flex-col justify-between w-full md:mx-auto">
        <!-- Top Section with Logo and Button -->
        <div class="flex-1 flex flex-col items-center justify-center p-6 pt-12">
            <!-- Logo and Title -->
            <div class="text-center mb-8">
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

            <!-- Play Now Button -->
            <div class="w-full max-w-xs">
                <button
                    class="w-full cursor-pointer bg-[#FF6B00] hover:bg-[#E55A00] text-white font-bold py-4 px-6 rounded-xl text-xl transition-all duration-150 transform border-b-[6px] border-[#CC5500] {buttonPressed
                        ? 'translate-y-1.5'
                        : ''}"
                    style="font-family: 'Nunito', sans-serif;  {!buttonPressed &&
                        'box-shadow: 0 6px 0 #CC5500;'}"
                    class:border-b-4={buttonPressed}
                    on:click={handlePlayNow}
                >
                    Play Now
                </button>
            </div>
        </div>

        <!-- Bottom Section with Illustration -->
        <div class="relative h-64 w-full"></div>
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

    .translate-y-1 {
        transform: translateY(4px);
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
