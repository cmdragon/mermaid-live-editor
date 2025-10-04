<script lang="ts">
  import FloatingToolbar from '$/components/FloatingToolbar.svelte';
  import Share from '$/components/Share.svelte';
  import { Button } from '$/components/ui/button';
  import { Toggle } from '$/components/ui/toggle';
  import { TID } from '$/constants';
  import { mode, setMode } from 'mode-watcher';
  import HistoryIcon from '~icons/material-symbols/history';
  import ThemeIcon from './ThemeIcon.svelte';

  interface Props {
    isHistoryOpen?: boolean;
  }

  let { isHistoryOpen = $bindable(false) }: Props = $props();
</script>

<FloatingToolbar>
  <Toggle bind:pressed={isHistoryOpen} size="sm" title="History">
    <HistoryIcon />
  </Toggle>
  <Share iconOnly={true} />
  <Button
    variant="ghost"
    size="icon"
    data-testid={TID.themeToggleButton}
    title="Switch to {$mode === 'dark' ? 'light' : 'dark'} theme"
    class="[&_svg]:size-5"
    onclick={() => setMode($mode === 'dark' ? 'light' : 'dark')}>
    <ThemeIcon />
  </Button>
</FloatingToolbar>
