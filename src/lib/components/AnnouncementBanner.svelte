<script>
    let { message = "" } = $props();
</script>

{#if message && message.length > 0}
<div class="w-full bg-muted/30 border-b overflow-hidden py-2 relative z-40">
    <div class="absolute inset-y-0 left-0 w-32 bg-gradient-to-r from-background via-background/80 to-transparent z-10 pointer-events-none"></div>
    <div class="absolute inset-y-0 right-0 w-32 bg-gradient-to-l from-background via-background/80 to-transparent z-10 pointer-events-none"></div>

    <div class="flex whitespace-nowrap animate-marquee">
        <div class="flex items-center gap-8 px-4 text-sm font-mono tracking-tight text-muted-foreground">
            {#each Array(4) as _}
                <span class="flex items-center gap-2">
                    <div class="h-1.5 w-1.5 rounded-full bg-primary animate-pulse"></div> 
                    {message}
                </span>
                <span class="opacity-30">/</span>
            {/each}
        </div>
    </div>
</div>
{/if}

<style>
    @keyframes marquee {
        0% { transform: translateX(0); }
        100% { transform: translateX(-25%); }
    }

    .animate-marquee {
        display: flex;
        width: max-content;
        animation: marquee 20s linear infinite;
    }

    .animate-marquee:hover {
        animation-play-state: paused;
    }
</style>