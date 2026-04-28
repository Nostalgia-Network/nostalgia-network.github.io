<script>
    import hero1 from "$lib/assets/slideshow/hero1.jpg";
    import hero2 from "$lib/assets/slideshow/hero2.jpg";
    import hero3 from "$lib/assets/slideshow/hero3.jpg";
    import { Button } from "$lib/components/ui/button/index.js";
    import { ChevronLeft, ChevronRight } from "@lucide/svelte";
    import { onMount } from "svelte";
    import config from "$lib/manager.json";
    import { SiDiscord } from '@icons-pack/svelte-simple-icons';

    // Slideshow state
    let currentImageIndex = $state(0);
    const images = [hero1, hero2, hero3];
    let interval;

    const startTimer = () => {
        clearInterval(interval);
        interval = setInterval(() => { currentImageIndex = (currentImageIndex + 1) % images.length; }, 7500);
    };

    const next = () => { currentImageIndex = (currentImageIndex + 1) % images.length; startTimer(); };
    const prev = () => { currentImageIndex = (currentImageIndex - 1 + images.length) % images.length; startTimer(); };
    const goTo = (i) => { currentImageIndex = i; startTimer(); };

    onMount(() => {
        startTimer();
        return () => clearInterval(interval);
    });
</script>

<section class="relative w-full h-175 overflow-hidden bg-background">
    <div class="absolute inset-0 z-0 w-full h-full" style:mask-image="linear-gradient(to bottom, black calc(100% - 300px), transparent 100%)" style:-webkit-mask-image="linear-gradient(to bottom, black calc(100% - 300px), transparent 100%)">
        {#each images as img, i}
            <img src={img} alt="Hero {i}" class="absolute inset-0 w-full h-full object-cover object-center transition-opacity duration-1500 {i === currentImageIndex ? 'opacity-100' : 'opacity-0'}" />
        {/each}
        <div class="absolute inset-0 bg-linear-to-r from-background via-background/40 to-transparent"></div>
        <div class="absolute inset-0 bg-background/10 dark:hidden"></div>
    </div>
    
    <div class="relative z-10 container mx-auto h-full flex flex-col justify-center px-6 lg:px-12">
        <div class="max-w-2xl space-y-6">
            <h1 
                class="text-6xl font-['Pacifico',_cursive] tracking-wide py-4 leading-tight"
                style:background="linear-gradient(90deg, #ffaa00 0%, #ff00ff 50%, #9900ff 100%)"
                style:-webkit-background-clip="text"
                style:-webkit-text-fill-color="transparent"
            >
                Welcome!
            </h1>
            <p class="text-lg text-foreground leading-relaxed">All of our game servers are privately owned, facilitated, and maintained, have a 98% uptime, and are backed up nightly at midnight (PST). Please join our Discord server get notified about changes and join the growing community!</p>
            <div class="pt-4">
                <Button size="lg" class="px-6 py-6 text-lg transition-transform" href={config.discord} target="_blank">
                    <SiDiscord class="mr-2 size-5" />
                    Join the Discord!
                </Button>
            </div>
        </div>
    </div>

    <div class="absolute bottom-12 left-1/2 -translate-x-1/2 z-20 flex items-center gap-4 px-4 py-2">
        <button onclick={prev} class="text-foreground/50 hover:text-primary transition-colors"><ChevronLeft size={20} /></button>
        <div class="flex gap-2.5">
            {#each images as _, i}
                <button onclick={() => goTo(i)} class="size-2 rotate-45 transition-all {i === currentImageIndex ? 'scale-100 bg-primary' : 'scale-75 bg-transparent border border-foreground/50 hover:border-primary'}"></button>
            {/each}
        </div>
        <button onclick={next} class="text-foreground/50 hover:text-primary transition-colors"><ChevronRight size={20} /></button>
    </div>
</section>