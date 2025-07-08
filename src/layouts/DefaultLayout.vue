<script setup lang="ts">
import {computed} from 'vue'
import Navbar from "@/components/Navbar.vue";
import {useUtilityStore} from '@/stores/utilityStore'
import {useWindowResize} from '@/composables/useWindowResize'

const {isLargePad} = useWindowResize();

const utilityStore = useUtilityStore()
const isMenuShow = computed(() => utilityStore.isMenuShow)
</script>

<template>
  <main>
    <div
      class="position-relative min-h-screen"
    >
      <slot></slot>
    </div>
  </main>
</template>