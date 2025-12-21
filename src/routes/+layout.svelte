<script>
    let { children } = $props();
    import "../app.css";
</script>

<div class="min-h-screen bg-slate-50">
    <div class="max-w-7xl mx-auto md:p-6 p-4">
        {@render children()}
    </div>
</div>
