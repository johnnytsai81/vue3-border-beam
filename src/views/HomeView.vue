<template>
  <div class="relative bg-gray-900 w-screen h-screen p-8">
    <div class="grid gap-1"
         :class="{
        'grid-cols-1': gridSize === 1,
        'grid-cols-3': gridSize === 3,
        'grid-cols-5': gridSize === 5,
        'grid-cols-10': gridSize === 10
      }">
      <!--      <BorderBeam-->
      <!--        v-for="index in totalItems"-->
      <!--        :key="`${gridKey}-${index}`"-->
      <!--        class="w-full aspect-square"-->
      <!--        :border-width="2"-->
      <!--        border-color="#ffffff"-->
      <!--        :duration="2"-->
      <!--        :size="30"-->
      <!--        :show-beam="randomSelectedIndexes.includes(index)"-->
      <!--      />-->
      <BorderBeamB
        v-for="index in totalItems"
        :key="`${gridKey}-${index}`"
        class="w-full aspect-square"
        :border-width="2"
        border-color="#ffffff"
        background="#555555"
        :duration="2"
        :size="10"
        :show-beam="randomSelectedIndexes.includes(index)"
        :shape="displayShape"
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
    <div class="mt-4 flex gap-4">
      <label class="flex items-center gap-2 text-white cursor-pointer">
        <input
          type="radio"
          name="displayMode"
          value="all"
          v-model="displayMode"
          class="w-4 h-4"
        />
        <span>All</span>
      </label>
      <label class="flex items-center gap-2 text-white cursor-pointer">
        <input
          type="radio"
          name="displayMode"
          value="random"
          v-model="displayMode"
          class="w-4 h-4"
        />
        <span>Random</span>
      </label>
    </div>
    <div class="mt-4 flex gap-4">
      <label class="flex items-center gap-2 text-white cursor-pointer">
        <input
          type="radio"
          name="displayShape"
          value="heart"
          v-model="displayShape"
          class="w-4 h-4"
        />
        <span>Heart</span>
      </label>
      <label class="flex items-center gap-2 text-white cursor-pointer">
        <input
          type="radio"
          name="displayShape"
          value="rectangle"
          v-model="displayShape"
          class="w-4 h-4"
        />
        <span>Rectangle</span>
      </label>
    </div>
  </div>
</template>

<script setup lang="ts">
// import BorderBeamA from "@/components/BorderBeamA.vue";
import BorderBeamB from "@/components/BorderBeamB.vue";
import {ref, computed} from 'vue'

const gridSize = ref(1)
const gridKey = ref(0)

const displayMode = ref('all')
const displayShape = ref('heart')

// Grid數量
const totalItems = computed(() => gridSize.value * gridSize.value)

// 計算要顯示的數量
const randomBeamCount = computed(() => {
  if (displayMode.value === 'all') return totalItems.value
  return gridSize.value
})

const randomSelectedIndexes = computed(() => {
  // 如果為all則顯示全部
  if (displayMode.value === 'all') {
    const allIndexes = []
    for (let i = 1; i <= totalItems.value; i++) {
      allIndexes.push(i)
    }
    return allIndexes
  }

  const indexes: number[] = []
  const total = totalItems.value
  const count = randomBeamCount.value

  // 生成隨機數且不重複
  while (indexes.length < count && indexes.length < total) {
    const randomIndex = Math.floor(Math.random() * total) + 1
    if (!indexes.includes(randomIndex)) {
      indexes.push(randomIndex)
    }
  }

  return indexes
})

// 切grid數值
const setGrid = (size: number) => {
  gridSize.value = size
  gridKey.value++
}
</script>