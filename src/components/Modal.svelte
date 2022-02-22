<script>
  import { blur, fade, fly } from "svelte/transition";
</script>

<div class="modal-container" transition:fade={{ delay: 250 }}>
  <div class="modal-content" transition:fly={{ y: 200, duration: 1000 }}>
    <slot />
  </div>
</div>
