<script context="module">
  let requestQueue = [];
  let isProcessing = false;
  async function processQueue() { if (isProcessing || requestQueue.length === 0) return; isProcessing = true; while (requestQueue.length > 0) { const { task } = requestQueue.shift(); await task(); await new Promise(resolve => setTimeout(resolve, 400)); } isProcessing = false; }
  function enqueue(task) { requestQueue.push({ task }); processQueue(); }
</script>

<script>
  import { onMount, onDestroy } from 'svelte';
  import { dev } from '$app/environment';
  import { Check, Play, Copy, HeartPulse, Map, ServerCrash, Gamepad2, Tag, Ghost, Wifi, BookOpen } from '@lucide/svelte';
  import GuideModal from './GuideModal.svelte';
  export let ID;
  export let gameSlug = "unturned";

  let info = null;
  let refreshTimer;
  let isModalOpen = false;
  let guideContent = "";
  let hasGuide = false;

  const dummyData = { name: "Development Server [US-East]", version: "1.34.2", connect: "0.0.0.0:0000", numplayers: 8, maxplayers: 16, map: "Russia", status: true, last_update: Math.floor(Date.now() / 1000), id: ID };
  const targetUrl = `https://api.gamemonitoring.net/servers/${ID}`;
  const proxyUrl = `https://api.cors.lol/?url=${encodeURIComponent(targetUrl)}`;

  async function fetchServerData() {
    if (dev) { info = dummyData; return; }
    enqueue(async () => {
      try {
        const response = await fetch(proxyUrl);
        if (response.status === 429) { setTimeout(fetchServerData, 5000); return; }
        if (!response.ok) throw new Error('Fetch failed');
        const data = await response.json();
        if (data?.response) { info = data.response; scheduleRefresh(300000); }
      } catch (err) { setTimeout(fetchServerData, 5000); }
    });
  }

  async function checkGuideExists() {
    try {
      const response = await fetch(`/docs/${ID}.md`, { method: 'HEAD' });
      hasGuide = response.ok;
    } catch { hasGuide = false; }
  }
  
  async function openGuide() {
    try {
      const res = await fetch(`/docs/${ID}.md`);
      guideContent = await res.text();
    } catch (e) {
      guideContent = "# Error\nCould not load documentation for this server.";
    }
    isModalOpen = true;
  }

  function scheduleRefresh(ms) { clearTimeout(refreshTimer); refreshTimer = setTimeout(fetchServerData, ms); }
  
  onMount(() => {
    fetchServerData();
    checkGuideExists();
  });
  
  onDestroy(() => clearTimeout(refreshTimer));
  $: isOnline = info?.status === true;
  let copied = false;
  async function copyToClipboard(text) { if (!text) return; try { await navigator.clipboard.writeText(text); copied = true; setTimeout(() => (copied = false), 2000); } catch (err) { console.error(err); } }
  function timeAgo(t) { if (!t) return 'Never'; const s = Math.floor((Date.now() - t * 1000) / 1000); if (s < 60) return 'Just now'; const intervals = { year: 31536000, month: 2592000, week: 604800, day: 86400, hour: 3600, minute: 60 }; for (let [u, v] of Object.entries(intervals)) { const c = Math.floor(s / v); if (c >= 1) return `${c} ${u}${c > 1 ? 's' : ''} ago`; } }
</script>

<style>
  :global(.animate-sync-pulse) { animation: pulse 2.5s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
  :global(.animate-sync-breathe) { animation: breathe 2.5s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
  @keyframes pulse { 75%, 100% { transform: scale(1.5); opacity: 0; } }
  @keyframes breathe { 0%, 100% { opacity: 1; } 50% { opacity: .3; } }
</style>

<div class="flex items-stretch rounded-lg border border-border bg-card overflow-hidden transition-all shadow-sm hover:shadow-md {!isOnline && info ? 'opacity-50' : ''}">
  <div class="w-16 sm:w-20 shrink-0 flex items-center justify-start pl-4 sm:justify-center sm:pl-0">
    <div class="relative flex items-center justify-center">
      {#if isOnline}
        <HeartPulse class="absolute size-6 text-emerald-400 animate-sync-pulse" />
        <HeartPulse class="relative size-6 text-emerald-400 animate-sync-breathe" />
      {:else if info === null}
        <div class="size-6 rounded-full bg-muted animate-pulse" />
      {:else}
        <ServerCrash class="relative size-6 text-red-500 animate-pulse" />
      {/if}
    </div>
  </div>

  <div class="grow py-4 flex items-center gap-8 min-w-0">
    <h4 class="font-medium tracking-wide text-foreground whitespace-nowrap overflow-hidden text-ellipsis">{info?.name || 'Loading...'}</h4>
    <div class="flex items-center gap-4 text-xs text-muted-foreground font-mono min-w-0">
      <span class="text-[10px] font-mono px-3 py-0.5 rounded-full border whitespace-nowrap shrink-0 {info?.version ? 'bg-primary/10 text-primary border-primary/30' : 'bg-muted/10 text-muted-foreground border-border'}">
        <div class="flex items-center gap-1.5">
          <Tag size={10} class="opacity-90" />
          <span class="font-bold">{info?.version || '...'}</span>
        </div>
      </span>
      <span class="size-1 rounded-full bg-muted-foreground/10 shrink-0"></span>
      <button on:click={() => copyToClipboard(info?.connect)} class="group/copy flex items-center gap-2 px-3 py-1 rounded-full border border-border bg-secondary/20 text-muted-foreground hover:text-primary hover:border-primary/40 transition-all text-[11px] whitespace-nowrap shrink-0">
        {#if copied}
          <Check size={12} class="text-emerald-500 shrink-0" />
          <span class="text-emerald-500 font-bold">Copied!</span>
        {:else}
          <Wifi size={12} class="opacity-60 group-hover/copy:opacity-100 transition-opacity shrink-0" />
          <span>{info?.connect || '0.0.0.0:0000'}</span>
          <Copy size={10} class="ml-1 opacity-40 group-hover/copy:opacity-60 shrink-0" />
        {/if}
      </button>
      <span class="size-1 rounded-full bg-muted-foreground/10 shrink-0"></span>
      <div class="flex items-center gap-1 shrink-0 whitespace-nowrap"><Ghost class="w-3.5 h-3.5" /> {info?.numplayers || 0}/{info?.maxplayers || 0}</div>
      <span class="size-1 rounded-full bg-muted-foreground/10 shrink-0"></span>
      <div class="flex items-center gap-1 truncate max-w-50"><Map class="w-3.5 h-3.5 shrink-0" /><span class="truncate">{info?.map || 'Loading...'}</span></div>
      <span class="size-1 rounded-full bg-muted-foreground/10 shrink-0"></span>
      <div class="flex items-center text-[11px] text-muted-foreground/30 font-medium uppercase tracking-tight leading-none shrink-0 whitespace-nowrap">
        Heartbeat: <span class="text-primary ml-1 mr-8">{timeAgo(info?.last_update)}</span>
      </div>
    </div>
  </div>

  {#if hasGuide}
    <div class="flex items-center px-4">
      <button on:click={openGuide} class="group/guide flex items-center gap-2 px-3 py-1 rounded-full border border-border bg-secondary/20 text-muted-foreground hover:text-primary hover:border-primary/40 transition-all text-[10px] font-bold uppercase tracking-wider whitespace-nowrap shrink-0">
        <span>Guide</span>
        <BookOpen size={12} class="opacity-40 group-hover/guide:opacity-60 transition-opacity shrink-0" />
      </button>
    </div>
  {/if}

  {#if info && isOnline}
    <a 
      href={`https://gamemonitoring.net/${gameSlug}/servers/${info.id}/connect`} 
      target="_blank" 
      class="group hidden lg:flex items-center justify-center bg-transparent text-muted-foreground/60 border-l border-border transition-all duration-500 ease-in-out shrink-0 relative overflow-hidden w-12 hover:w-36 hover:bg-primary hover:text-primary-foreground hover:border-primary"
    >
      <div class="flex items-center justify-center transition-all duration-500">
        <span class="font-bold uppercase tracking-widest text-xs whitespace-nowrap transition-all duration-500 max-w-0 opacity-0 mr-0 group-hover:max-w-xs group-hover:opacity-100 group-hover:mr-3">
          play now
        </span>
        <div class="relative size-4 flex items-center justify-center shrink-0">
          <div class="absolute transition-all duration-500 transform scale-100 opacity-100 rotate-0 group-hover:rotate-180 group-hover:scale-0 group-hover:opacity-0 flex items-center justify-center">
            <Play size={16} />
          </div>
          <div class="absolute transition-all duration-500 transform scale-0 opacity-0 -rotate-180 group-hover:rotate-0 group-hover:scale-110 group-hover:opacity-100 flex items-center justify-center">
            <Gamepad2 size={16} />
          </div>
        </div>
      </div>
    </a>
  {/if}
</div>

<GuideModal 
  isOpen={isModalOpen} 
  onClose={() => isModalOpen = false} 
  serverName={info?.name || 'Server'} 
  markdownContent={guideContent} 
/>