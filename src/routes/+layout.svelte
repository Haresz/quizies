<script>
    let { children } = $props();
    import "../app.css";
</script>

<div class="max-w-7xl mx-auto md:p-4 p-2">
    {@render children()}
</div>
