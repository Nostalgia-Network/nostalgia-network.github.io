<script>
    import * as Card from "$lib/components/ui/card";
    import ServerCard from "./ServerCard.svelte";
    import ServerCardOverlay from "./ServerCardBanner.svelte";
    import { Server, ExternalLink } from "@lucide/svelte";
    import { buttonVariants } from "$lib/components/ui/button";
    import { SiSteam } from '@icons-pack/svelte-simple-icons';
    
    export let title = "";
    export let url = "";
    export let servers = [];
    export let startIndex = 0;
    export let loading = false;

    $: serverCount = servers.length;
</script>

<Card.Root class="overflow-hidden border-border bg-card shadow-sm pb-2">
    <Card.Header>
        <div class="flex flex-row items-center justify-between w-full">
            <div class="flex items-center gap-4">
                <div class="flex items-center gap-2">
                    <Server class="w-4 h-4 text-primary" />
                    <span class="text-sm text-muted-foreground font-medium">{serverCount}</span>
                </div>
                <Card.Title class="text-xl font-bold tracking-tight text-card-foreground">{title}</Card.Title>
            </div>

            {#if url}
                <a 
                    href={url} 
                    target="_blank" 
                    rel="noopener noreferrer" 
                    class="flex items-center text-sm font-medium text-primary hover:underline underline-offset-4"
                >
                    View on Steam
                    <SiSteam class="ml-2 size-4" />
                </a>
            {/if}
        </div>
    </Card.Header>
    
    <Card.Content class="flex flex-col gap-4 overflow-x-auto pb-4">
        <div class="flex flex-col gap-4 min-w-max">
            {#if loading}
                {#each Array(serverCount || 3) as _}
                    <div class="h-20 w-full rounded-lg bg-muted/10 animate-pulse border border-border/50 flex items-center px-6 gap-4">
                        <div class="size-6 rounded-full bg-muted/20" />
                        <div class="h-4 w-48 rounded bg-muted/20" />
                        <div class="h-4 w-24 rounded bg-muted/20" />
                    </div>
                {/each}
            {:else}
                {#each servers as server, i}
                    {#if server.offlineReason}
                        <ServerCardOverlay reason={server.offlineReason}>
                            <ServerCard ID={server.id} index={startIndex + i} />
                        </ServerCardOverlay>
                    {:else}
                        <ServerCard ID={server.id} index={startIndex + i} />
                    {/if}
                {/each}
            {/if}
        </div>
    </Card.Content>
</Card.Root>