<script lang="ts">
  import Actions from '$/components/Actions.svelte';
  import Card from '$/components/Card/Card.svelte';
  import DiagramDocButton from '$/components/DiagramDocumentationButton.svelte';
  import Editor from '$/components/Editor.svelte';
  import History from '$/components/History/History.svelte';
  import PanZoomToolbar from '$/components/PanZoomToolbar.svelte';
  import Preset from '$/components/Preset.svelte';
  import SyncRoughToolbar from '$/components/SyncRoughToolbar.svelte';
  import * as Resizable from '$/components/ui/resizable';
  import { Switch } from '$/components/ui/switch';
  import * as Dialog from '$/components/ui/dialog';
  import VersionSecurityToolbar from '$/components/VersionSecurityToolbar.svelte';
  import View from '$/components/View.svelte';
  import type { EditorMode, Tab } from '$/types';
  import { PanZoomState } from '$/util/panZoom';
  import { stateStore, updateCodeStore } from '$/util/state';
  import { logEvent } from '$/util/stats';
  import { initHandler } from '$/util/util';
  import { onMount } from 'svelte';
  import CodeIcon from '~icons/custom/code';
  import GearIcon from '~icons/material-symbols/settings-outline-rounded';
  import CloseIcon from '~icons/material-symbols/close-rounded';

  const panZoomState = new PanZoomState();

  const tabSelectHandler = (tab: Tab) => {
    const editorMode: EditorMode = tab.id === 'code' ? 'code' : 'config';
    updateCodeStore({ editorMode });
  };

  const editorTabs: Tab[] = [
    {
      icon: CodeIcon,
      id: 'code',
      title: 'Code'
    },
    {
      icon: GearIcon,
      id: 'config',
      title: 'Config'
    }
  ];

  let width = $state(0);
  let isMobile = $derived(width < 640);
  let isViewMode = $state(true);

  onMount(async () => {
    await initHandler();
    window.addEventListener('appinstalled', () => {
      logEvent('pwaInstalled', { isMobile });
    });
  });

  let isHistoryOpen = $state(false);
  let showPromoBanner = $state(true);

  const closeBanner = () => {
    showPromoBanner = false;
  };

  let editorPane: Resizable.Pane | undefined;
  $effect(() => {
    if (isMobile) {
      editorPane?.resize(50);
    }
  });
</script>

<!-- Mobile History Dialog -->
{#if isMobile}
  <Dialog.Root bind:open={isHistoryOpen}>
    <Dialog.Content class="h-[90vh] max-w-[95vw] p-0">
      <div class="h-full overflow-hidden">
        <History />
      </div>
    </Dialog.Content>
  </Dialog.Root>
{/if}

<div class="flex h-full flex-col overflow-hidden">
  <!-- Promotion Banner -->
  {#if showPromoBanner}
    <div
      class="relative flex shrink-0 items-center justify-center bg-gradient-to-r from-blue-600 to-purple-600 px-4 py-3 text-center text-sm font-medium text-white">
      <a
        href="https://tools.cmdragon.cn/"
        target="_blank"
        rel="noopener noreferrer"
        class="flex-1 transition-all hover:underline">
        🚀 Explore more useful tools - Visit CMDragon Tools
      </a>
      <button
        onclick={closeBanner}
        class="absolute right-2 rounded p-1 transition-all hover:bg-white/20"
        title="Close banner"
        aria-label="Close promotional banner">
        <CloseIcon class="size-5" />
      </button>
    </div>
  {/if}
  <div class="flex flex-1 flex-col overflow-hidden" bind:clientWidth={width}>
    {#if isMobile}
      <div
        class="flex shrink-0 items-center justify-center gap-2 border-b border-border bg-card px-4 py-3">
        <span class="text-sm font-medium">Edit</span>
        <Switch
          id="editorMode"
          class="data-[state=checked]:bg-accent"
          bind:checked={isViewMode}
          onclick={() => {
            logEvent('mobileViewToggle');
          }} />
        <span class="text-sm font-medium">View</span>
      </div>
    {/if}
    <div
      class={[
        'flex-1 overflow-hidden',
        isMobile && ['w-[200%] duration-300', isViewMode && '-translate-x-1/2']
      ]}>
      <Resizable.PaneGroup
        direction="horizontal"
        autoSaveId="liveEditor"
        class="gap-4 p-2 sm:gap-0 sm:p-6 sm:pt-0">
        <Resizable.Pane bind:this={editorPane} defaultSize={30} minSize={15}>
          <div class="flex h-full flex-col gap-4 sm:gap-6">
            <Card
              onselect={tabSelectHandler}
              isOpen
              tabs={editorTabs}
              activeTabID={$stateStore.editorMode}
              isClosable={false}>
              {#snippet actions()}
                <DiagramDocButton />
              {/snippet}
              <Editor {isMobile} />
            </Card>

            <div class="group flex flex-wrap justify-between gap-4 sm:gap-6">
              <Preset />
              <Actions />
            </div>
          </div>
        </Resizable.Pane>
        <Resizable.Handle class="mr-1 hidden opacity-0 sm:block" />
        <Resizable.Pane minSize={15} class="relative flex h-full flex-1 flex-col overflow-hidden">
          <View {panZoomState} shouldShowGrid={$stateStore.grid} />
          <div class="absolute top-0 right-0"><PanZoomToolbar {panZoomState} /></div>
          <div class="absolute right-0 bottom-0"><VersionSecurityToolbar bind:isHistoryOpen /></div>
          <div class="absolute bottom-0 left-0 sm:left-5"><SyncRoughToolbar /></div>
        </Resizable.Pane>
        {#if isHistoryOpen}
          <Resizable.Handle class="ml-1 hidden opacity-0 sm:block" />
          <Resizable.Pane
            minSize={15}
            defaultSize={30}
            class="hidden h-full flex-grow flex-col sm:flex">
            <History />
          </Resizable.Pane>
        {/if}
      </Resizable.PaneGroup>
    </div>
  </div>
</div>
