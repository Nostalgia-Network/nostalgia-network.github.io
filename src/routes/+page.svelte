<script lang="ts">
  import { TreePalm } from "@lucide/svelte";
  import ServerList from "$lib/components/ServerList.svelte";
</script>

<main class="p-1 md:p-8">
  <div class="max-w-8xl mx-auto">
    <header class="mb-12 text-center md:text-left">
      <h1 class="flex items-center justify-start gap-3 text-4xl font-extrabold tracking-wide text-foreground mb-4">
        <TreePalm class="text-primary mr-2" size={48} strokeWidth={1.5} />
        <span
          class="text-4xl font-['Pacifico',_cursive] tracking-wide py-4 leading-tight"
          style:background="linear-gradient(90deg, #ffaa00 0%, #ff00ff 50%, #9900ff 100%)"
          style:-webkit-background-clip="text"
          style:-webkit-text-fill-color="transparent"
        >
          Servers
        </span>
      </h1>
    </header>

    <ServerList />
  </div>
</main>