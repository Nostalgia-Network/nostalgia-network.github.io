<script lang="ts">
    export let reason: string = "OFFLINE";
</script>

<div class="relative group overflow-hidden rounded-lg border border-border bg-card pointer-events-none">
    <div class="opacity-40 grayscale-[0.8] select-none">
        <slot />
    </div>

    <div class="absolute inset-0 z-10" style="backdrop-filter: blur(4px); -webkit-backdrop-filter: blur(4px); border-radius: var(--radius);"></div>

    <div class="absolute inset-0 z-20 flex items-center justify-center cursor-not-allowed">
        <span class="text-md font-bold tracking-[0.25em] text-red-400 uppercase animate-pulse">
            {reason}
        </span>
    </div>
</div>