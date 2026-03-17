<script lang="ts">
  import * as Dialog from '$/components/ui/dialog';
  import { onMount } from 'svelte';

  interface Props {
    open: boolean;
  }

  let { open = $bindable(false) }: Props = $props();

  onMount(() => {
    // 页面加载时自动显示弹窗
    open = true;
  });
</script>

<Dialog.Root bind:open>
  <Dialog.Content class="p-6 sm:max-w-md">
    <Dialog.Header>
      <Dialog.Title class="text-center text-2xl font-bold">关注微信公众号</Dialog.Title>
      <Dialog.Description class="text-center">获取更多实用工具和教程</Dialog.Description>
    </Dialog.Header>
    <div class="flex flex-col items-center gap-4">
      <div class="flex justify-center">
        <img src="/wechat_qrcode.webp" alt="微信公众号二维码" class="h-48 w-48 object-contain" />
      </div>
      <div class="text-center">
        <p class="font-medium">扫码关注</p>
        <p class="text-sm text-muted-foreground">获取更多实用工具</p>
      </div>
    </div>
    <Dialog.Footer class="flex justify-center">
      <button
        class="inline-flex h-10 items-center justify-center rounded-md bg-primary px-4 py-2 text-sm font-medium whitespace-nowrap text-primary-foreground ring-offset-background transition-colors hover:bg-primary/90 focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 focus-visible:outline-none disabled:pointer-events-none disabled:opacity-50"
        onclick={() => (open = false)}>
        关闭
      </button>
    </Dialog.Footer>
  </Dialog.Content>
</Dialog.Root>
