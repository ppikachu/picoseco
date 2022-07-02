<template>
  <div
    class="absolute w-full h-full flex flex-col items-end justify-start tracking-widest modal-container text-slate-400"
  >
    <div class="modal-body p-24 text-4xl">
      <slot name="body">default body</slot>
    </div>
  </div>
</template>

<script setup>
</script>