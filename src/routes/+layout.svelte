<script>
    import { page } from '$app/stores';
    import config from "$lib/manager.json";
    import "./layout.css";
    import favicon from "$lib/assets/favicon.svg";
    import HeroSlideshow from "$lib/components/HeroSlideshow.svelte";
    import AnnouncementBanner from "$lib/components/AnnouncementBanner.svelte";
    import { ModeWatcher, toggleMode } from "mode-watcher";
    import { Button } from "$lib/components/ui/button/index.js";
    import * as NavigationMenu from "$lib/components/ui/navigation-menu/index.js";
    import { SiDiscord, SiGithub } from '@icons-pack/svelte-simple-icons';
    import { Copyright, GamepadDirectional, Eclipse, MoonIcon, Flame, CodeXml } from "@lucide/svelte";

    let { children } = $props();
    const rows = Array(60).fill(0);
    const cols = Array(120).fill(0);

    let isHome = $derived($page.url.pathname === '/');
    const announcementMessage = $derived(config.announcement);
</script>

<svelte:head>
    <link rel="icon" href={favicon} />
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
</svelte:head>

<ModeWatcher defaultMode="dark" />

<NavigationMenu.Root class="max-w-full w-full justify-start px-4 md:px-8 py-2 sticky top-0 z-50 bg-background border-b">
    <NavigationMenu.List class="max-w-full w-full flex items-center">
        <a href="/" class="flex items-center space-x-2">
            <img src="/icon.svg" alt="Nostalgia Network" class="h-10 w-auto pr-4" loading="eager" />
            <span style:background="linear-gradient(90deg, #ffaa00 0%, #ff00ff 50%, #9900ff 100%)" style:-webkit-background-clip="text" style:-webkit-text-fill-color="transparent" class="text-lg sm:text-xl md:text-2xl font-['Pacifico',_cursive] tracking-wide py-4 leading-none">
                Nostalgia Network
            </span>
        </a>
    </NavigationMenu.List>
    
    <div class="ml-auto"></div>

    <div class="flex gap-x-4">
        <Button variant="ghost" size="icon" href={config.discord} target="_blank">
            <SiDiscord size={16} className="text-foreground stroke-[2.5px]" aria-hidden="true" />
        </Button>

        <Button variant="ghost" size="icon" href="https://github.com/nostalgia-network" target="_blank">
            <SiGithub size={16} className="text-foreground stroke-[2.5px]" aria-hidden="true" />
        </Button>

        <Button onclick={toggleMode} variant="ghost" size="icon">
            <Eclipse class="h-[1.2rem] w-[1.2rem] scale-100 rotate-0 transition-all! dark:scale-0 dark:-rotate-90" />
            <MoonIcon class="absolute h-[1.2rem] w-[1.2rem] scale-0 rotate-90 transition-all! dark:scale-100 dark:rotate-0" />
        </Button>
    </div>
</NavigationMenu.Root>

<AnnouncementBanner message={announcementMessage} />

<div class="relative flex flex-col items-center">
    {#if isHome}
        <HeroSlideshow />
    {/if}

    <div class="w-full relative z-10" style:margin-top="-50px" style:mask-image="linear-gradient(to top, black calc(100% - 150px), transparent 100%)" style:-webkit-mask-image="linear-gradient(to top, black calc(100% - 150px), transparent 100%)">
        <div class="relative min-h-screen w-full overflow-hidden bg-background">
            <div class="absolute left-0 top-0 flex h-[200vh] w-[200vw] -ml-[50vw] -mt-[50vh] rotate-12 flex-col justify-center gap-8 pointer-events-none">
                {#each rows as _, rowIndex}
                    <div class="flex w-full justify-center gap-8">
                        {#each cols as _, colIndex}
                            <div class="text-foreground/10 dark:text-foreground/15">
                                {#if (rowIndex + colIndex) % 2 === 0}
                                    <Flame size={32} strokeWidth={1.5} />
                                {:else}
                                    <div class="flex items-center justify-center">
                                        <GamepadDirectional size={32} strokeWidth={1.5} />
                                    </div>
                                {/if}
                            </div>
                        {/each}
                    </div>
                {/each}
            </div>

            <div class="relative z-10 flex flex-col gap-8 w-full max-w-400 mx-auto py-32 px-4">
                {@render children?.()}
            </div>
        </div>
    </div>
</div>

<footer class="w-full border-t bg-background py-8 text-center">
    <div class="container mx-auto flex flex-col items-center gap-2">
        <div class="flex items-center gap-1.5 text-xs text-muted-foreground/60">
            <span>Code licensed under <a href="https://github.com/Perdyx/nostalgia/blob/main/LICENSE" target="_blank" class="font-medium underline underline-offset-4">MIT License</a>.</span>
            <Copyright class="size-3" />
            <span>{new Date().getFullYear()}</span>
            Nostalgia Network
        </div>
    </div>
</footer>