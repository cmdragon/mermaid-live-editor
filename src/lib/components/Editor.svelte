<script lang="ts">
  import DesktopEditor from '$/components/DesktopEditor.svelte';
  import MobileEditor from '$/components/MobileEditor.svelte';
  import { TID } from '$/constants';
  import { stateStore, updateCode, updateConfig } from '$lib/util/state';
  import ExclamationCircleIcon from '~icons/material-symbols/error-outline-rounded';

  let { isMobile }: { isMobile: boolean } = $props();
  const onUpdate = (text: string) => {
    if ($stateStore.editorMode === 'code') {
      updateCode(text);
    } else {
      updateConfig(text);
    }
  };
</script>

<div class="flex h-full flex-col">
  {#if isMobile}
    <MobileEditor {onUpdate} />
  {:else}
    <DesktopEditor {onUpdate} />
  {/if}
  {#if $stateStore.error instanceof Error}
    <div class="flex flex-col text-sm" data-testid={TID.errorContainer}>
      <div class="flex items-center gap-2 bg-slate-900 p-2 text-white">
        <ExclamationCircleIcon class="size-6 text-destructive" aria-hidden="true" />
        <p>Syntax error</p>
      </div>
      <output class="max-h-32 overflow-auto bg-muted p-2" name="mermaid-error" for="editor">
        <pre>{$stateStore.error?.toString()}</pre>
      </output>
    </div>
  {/if}
</div>
