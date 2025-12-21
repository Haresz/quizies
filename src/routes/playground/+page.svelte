<script>
    import Button3D from "$lib/components/Buttton3D.svelte";

    function handlePlayNow() {
        console.log("Play Now clicked!");
    }

    function handleLearnMore() {
        console.log("Learn More clicked!");
    }

    function handleSubscribe() {
        console.log("Subscribe clicked!");
    }
</script>

<div class="min-h-screen bg-gray-100 p-8">
    <div class="max-w-4xl mx-auto">
        <h1 class="text-3xl font-bold text-gray-800 mb-8">
            3D Button Component Examples
        </h1>

        <div class="space-y-8">
            <!-- Example 1: Default Play Now Button -->
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold mb-4">
                    Default Play Now Button
                </h2>
                <div class="w-full max-w-xs">
                    <Button3D text="Play Now" on:click={handlePlayNow} />
                </div>
            </div>

            <!-- Example 2: Small Learn More Button -->
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold mb-4">
                    Small Learn More Button
                </h2>
                <div class="w-full max-w-xs">
                    <Button3D
                        text="Learn More"
                        size="sm"
                        bgColor="#4CAF50"
                        on:click={handleLearnMore}
                    />
                </div>
            </div>

            <!-- Example 3: Large Subscribe Button with Custom Colors -->
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold mb-4">
                    Large Subscribe Button with Custom Colors
                </h2>
                <div class="w-full max-w-xs">
                    <Button3D
                        text="Subscribe"
                        size="lg"
                        bgColor="#9C27B0"
                        shadowColor="#6A1B9A"
                        textColor="#FFFFFF"
                        outlineWidth={8}
                        fullWidth={true}
                        on:click={handleSubscribe}
                    />
                </div>
            </div>

            <!-- Example 4: Warning Button -->
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold mb-4">Warning Button</h2>
                <div class="w-full max-w-xs">
                    <Button3D
                        text="Delete All"
                        size="md"
                        bgColor="#F44336"
                        textColor="#FFFFFF"
                        outlineWidth={4}
                        on:click={() => console.log("Delete All clicked!")}
                    />
                </div>
            </div>

            <!-- Example 5: Info Button -->
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold mb-4">Info Button</h2>
                <div class="w-full max-w-xs">
                    <Button3D
                        text="Get Info"
                        size="md"
                        bgColor="#2196F3"
                        textColor="#FFFFFF"
                        outlineWidth={5}
                        on:click={() => console.log("Get Info clicked!")}
                    />
                </div>
            </div>
        </div>
    </div>
</div>
