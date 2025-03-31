<template>
  <!-- Custom cursor component doesn't need visible elements -->
  <div class="cursor-styles"></div>
</template>

<script>
export default {
  name: 'CustomCursor',
  mounted() {
    // Adding the custom cursor classes to the body to apply globally
    document.body.classList.add('custom-cursor');
  },
  beforeUnmount() {
    // Cleanup when component is unmounted
    document.body.classList.remove('custom-cursor');
  }
}
</script>

<style>
/* Global styles for custom cursor */
.custom-cursor {
  cursor: url('/cursor/comet.png'), auto;
}

/* Apply hover cursor to interactive elements */
.custom-cursor a,
.custom-cursor button,
.custom-cursor [role="button"],
.custom-cursor input[type="submit"],
.custom-cursor input[type="button"],
.custom-cursor .clickable,
.custom-cursor select,
.custom-cursor label[for] {
  cursor: url('/cursor/eruption.png'), pointer !important;
}
</style>
