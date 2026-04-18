<template>
  <div class="slidev-layout default h-full flex flex-col px-14">
    <div class="flex-1 min-h-0 pt-10 pb-2">
      <slot />
    </div>
    <footer
      v-if="$slots.footer"
      class="shrink-0 border-t border-gray-200 py-3 text-center text-sm text-gray-600"
    >
      <slot name="footer" />
    </footer>
    <div v-else class="shrink-0 h-10" />
  </div>
</template>
