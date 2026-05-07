<script lang="ts">
  import { Button } from '$/components/ui/button';
  import { TID } from '$/constants';
  import AutoAwesomeIcon from '~icons/material-symbols/auto-awesome';
</script>

<Button
  variant="ghost"
  data-testid={TID.diagramDocumentationButton}
  href="https://tools.cmdragon.cn/apps?category=ai_chat"
  target="_blank"
  title="AI Tools">
  <AutoAwesomeIcon />
  AI
</Button>
