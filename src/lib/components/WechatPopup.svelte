<script lang="ts">
  import * as Dialog from '$/components/ui/dialog';
  import { Button } from '$/components/ui/button';
  import { Input } from '$/components/ui/input';

  interface Props {
    open: boolean;
    message?: string;
    onVerify?: (code: string) => Promise<boolean>;
    onVerifySuccess?: () => void;
  }

  let { open = $bindable(false), message = '', onVerify, onVerifySuccess }: Props = $props();

  let verifyCode = $state('');
  let isVerifying = $state(false);
  let errorMessage = $state('');

  const handleVerify = async () => {
    if (!verifyCode.trim()) {
      errorMessage = '请输入验证码';
      return;
    }

    isVerifying = true;
    errorMessage = '';

    try {
      const success = await onVerify?.(verifyCode.trim());
      if (success) {
        onVerifySuccess?.();
        open = false;
        verifyCode = '';
        errorMessage = '';
      } else {
        errorMessage = '验证码错误或已失效';
      }
    } catch {
      errorMessage = '验证失败，请重试';
    } finally {
      isVerifying = false;
    }
  };

  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Enter') {
      handleVerify();
    }
  };

  const handleClose = () => {
    open = false;
    verifyCode = '';
    errorMessage = '';
  };
</script>

<Dialog.Root bind:open>
  <Dialog.Content class="p-6 sm:max-w-md" onkeydown={handleKeyDown}>
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
      {#if message}
        <div class="rounded-md bg-yellow-50 p-3 text-sm text-yellow-800">
          {message}
        </div>
      {/if}
      <div class="flex w-full flex-col gap-2">
        <Input
          type="text"
          placeholder="请输入验证码"
          bind:value={verifyCode}
          onkeydown={handleKeyDown} />
        {#if errorMessage}
          <p class="text-sm text-red-500">{errorMessage}</p>
        {/if}
        <Button onclick={handleVerify} disabled={isVerifying}>
          {#if isVerifying}
            验证中...
          {:else}
            验证
          {/if}
        </Button>
      </div>
    </div>
    <Dialog.Footer class="flex justify-center">
      <Button variant="outline" onclick={handleClose}>关闭</Button>
    </Dialog.Footer>
  </Dialog.Content>
</Dialog.Root>
