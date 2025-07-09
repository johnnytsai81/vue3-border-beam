<template>
  <div class="relative bg-gray-900 w-screen h-screen p-8">
    <div class="grid gap-1"
         :class="{
        'grid-cols-1': gridSize === 1,
        'grid-cols-3': gridSize === 3,
        'grid-cols-5': gridSize === 5,
        'grid-cols-10': gridSize === 10
      }">
      <BorderBeam
        v-for="index in totalItems"
        :key="`${gridKey}-${index}`"
        class="w-full aspect-square"
        :border-width="2"
        border-color="#ffffff"
        :duration="2"
        :size="30"
      />
    </div>
    <div class="mt-4 flex gap-2">
      <button
        class="bg-white px-3 py-1 cursor-pointer rounded transition-colors"
        @click="setGrid(1)"
      >
        1x1
      </button>
      <button
        class="bg-white px-3 py-1 cursor-pointer rounded transition-colors"
        @click="setGrid(3)"
      >
        3x3
      </button>
      <button
        class="bg-white px-3 py-1 cursor-pointer rounded transition-colors"
        @click="setGrid(5)"
      >
        5x5
      </button>
      <button
        class="bg-white px-3 py-1 cursor-pointer rounded transition-colors"
        @click="setGrid(10)"
      >
        10x10
      </button>
    </div>
  </div>
</template>

<script setup>
import BorderBeam from "@/components/BorderBeam.vue";
import {ref, computed} from 'vue'

const gridSize = ref(1)
const gridKey = ref(0)

const totalItems = computed(() => gridSize.value * gridSize.value)

const setGrid = (size) => {
  gridSize.value = size
  gridKey.value++
}
</script>