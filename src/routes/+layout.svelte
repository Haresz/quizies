<script>
    let { children } = $props();
    import "../app.css";
</script>

<div class="max-w-7xl md:p-4 p-2">
    {@render children()}
</div>
