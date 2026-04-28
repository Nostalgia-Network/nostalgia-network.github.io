<script lang="ts">
    import GameCard from "./GameCard.svelte";
    import config from "$lib/manager.json";
</script>

<div class="grid gap-6">
    {#each config.games as game}
        <GameCard title={game.title} url={game.url} servers={game.servers} startIndex={0} />
    {/each}
</div>