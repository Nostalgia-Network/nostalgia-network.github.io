<script>
  import { marked } from 'marked';
  import * as Dialog from "$lib/components/ui/dialog/index.js";
  import { Button } from "$lib/components/ui/button/index.js";

  let { isOpen = false, serverName = "", markdownContent = "", onClose } = $props();

  marked.use({ breaks: true });
  let renderedHtml = $derived(marked.parse(markdownContent || ''));

  function handleOpenChange(open) {
    if (!open) onClose();
  }
</script>

<Dialog.Root open={isOpen} onOpenChange={handleOpenChange}>
  <Dialog.Content class="sm:max-w-[700px] gap-0 p-0 overflow-hidden bg-card text-card-foreground">
    <Dialog.Header class="px-6 py-4 border-b border-border bg-muted/30 text-left">
      <Dialog.Title class="text-lg font-bold">{serverName}</Dialog.Title>
    </Dialog.Header>

    <div class="max-h-[69vh] overflow-y-auto py-4 px-8">
      <div class="prose prose-sm dark:prose-invert max-w-none">
        {@html renderedHtml}
      </div>
    </div>
  </Dialog.Content>
</Dialog.Root>