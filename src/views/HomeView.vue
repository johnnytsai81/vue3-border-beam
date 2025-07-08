<template>
  <div class="relative bg-gray-900 w-screen h-screen p-8">
    <div class="grid grid-cols-3">
      <BorderBeam
        class="w-full aspect-square"
        :anchor="45"
        :border-width="3"
        border-color="#ffffff"
        :duration="2"
        :size="30"
      />
    </div>
  </div>
</template>

<script setup>
import BorderBeam from "@/components/BorderBeam.vue";
</script>
