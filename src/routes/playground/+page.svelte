<script>
    import SelectOptions from "$lib/components/SelectOptions.svelte";
    import { Bell } from "@lucide/svelte";

    // Example data
    const countries = [
        { value: "us", label: "United States" },
        { value: "ca", label: "Canada" },
        { value: "uk", label: "United Kingdom" },
        { value: "fr", label: "France" },
        { value: "de", label: "Germany" },
        { value: "jp", label: "Japan" },
        { value: "au", label: "Australia" },
    ];

    const fruits = [
        {
            value: "apple",
            label: "Apple",
            description: "A sweet red fruit",
            icon: Bell,
        },
        {
            value: "banana",
            label: "Banana",
            description: "A yellow tropical fruit",
        },
        { value: "orange", label: "Orange", description: "A citrus fruit" },
        { value: "grape", label: "Grape", description: "Small sweet berries" },
        {
            value: "strawberry",
            label: "Strawberry",
            description: "A red berry fruit",
        },
    ];

    let selectedCountry = $state("");
    let selectedFruits = $state([]);
    let searchableValue = $state("");
</script>

<div class="max-w-4xl mx-auto p-8">
    <h1 class="text-3xl font-bold mb-8">SelectOptions Component Demo</h1>

    <div class="space-y-8">
        <!-- Basic Select -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Basic Select</h2>
            <SelectOptions
                options={countries}
                bind:value={selectedCountry}
                label="Select a country"
                placeholder="Choose your country"
            />
            <p class="mt-2 text-sm text-gray-600">
                Selected: {selectedCountry}
            </p>
        </div>

        <!-- Multi-Select -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Multi-Select</h2>
            <SelectOptions
                options={fruits}
                bind:value={selectedFruits}
                label="Select fruits"
                placeholder="Choose your favorite fruits"
                multiple={true}
                clearable={true}
            />
            <p class="mt-2 text-sm text-gray-600">
                Selected: {selectedFruits.join(", ")}
            </p>
        </div>

        <!-- Searchable Select -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Searchable Select</h2>
            <SelectOptions
                options={countries}
                bind:value={searchableValue}
                label="Searchable country select"
                placeholder="Type to search..."
                searchable={true}
                clearable={true}
            />
            <p class="mt-2 text-sm text-gray-600">
                Selected: {searchableValue}
            </p>
        </div>

        <!-- Different Sizes -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Different Sizes</h2>
            <div class="space-y-4">
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Small size"
                    size="sm"
                />
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Medium size (default)"
                    size="md"
                    prefix={Bell}
                />
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Large size"
                    size="lg"
                />
            </div>
        </div>

        <!-- Different Variants -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Different Variants</h2>
            <div class="space-y-4">
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Default variant"
                    variant="default"
                />
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Outlined variant"
                    variant="outlined"
                />
                <SelectOptions
                    options={countries}
                    bind:value={selectedCountry}
                    label="Filled variant"
                    variant="filled"
                />
            </div>
        </div>

        <!-- With Error State -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Error State</h2>
            <SelectOptions
                options={countries}
                bind:value={selectedCountry}
                label="Country with error"
                placeholder="Please select a country"
                error="This field is required"
                required={true}
            />
        </div>

        <!-- Disabled State -->
        <div>
            <h2 class="text-xl font-semibold mb-4">Disabled State</h2>
            <SelectOptions
                options={countries}
                value="us"
                label="Disabled select"
                disabled={true}
            />
        </div>
    </div>
</div>
