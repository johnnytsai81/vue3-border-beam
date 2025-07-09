<template>
  <div
    class="border-beam"
    :style="cssVars"
  >
    <slot />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

// #region Props
interface Props {
  /** 邊框元素位置 (%) */
  anchor?: number;
  /** 邊框寬度 */
  borderWidth?: number;
  /** 邊框顏色，請使用包含 # 之 HEX 格式 */
  borderColor?: string;
  /** 邊框圓角 */
  borderRadius?: number;
  /** 動畫持續時間 (秒) */
  duration?: number;
  /** 邊框元素大小 (%) */
  size?: number;
}
// #endregion Props

const props = withDefaults(defineProps<Props>(), {
  anchor: 50,
  borderWidth: 1.5,
  borderColor: '#ffffff',
  borderRadius: 8,
  duration: 5,
  size: 200,
})

// 計算 CSS 變數
const cssVars = computed(() => ({
  '--anchor': props.anchor,
  '--border-width': props.borderWidth,
  '--border-color': props.borderColor,
  '--border-radius': props.borderRadius,
  '--duration': props.duration,
  '--size': props.size
}))
</script>

<style scoped lang="scss">
.border-beam {
  position: relative;
  inset: 0;
  border-radius: calc( var(--border-radius) * 1px);
  border: calc(var(--border-width) * 1px) solid #555555;
  mask: linear-gradient(transparent, transparent), linear-gradient(white, white);
  mask-clip: padding-box, border-box;
  mask-composite: intersect;
  &::before {
    content: '';
    position: absolute;
    aspect-ratio: 1;
    width: calc(var(--size) * 1%);
    animation: border-beam-reverse calc(var(--duration) * 1s) cubic-bezier(0.4, 0.14, 0.6, 0.86) infinite;
    background: var(--border-color);
    offset-anchor: calc(var(--anchor) * 1%) 90%;
    offset-path: rect(0 auto auto 0 round calc(var(--size) * 1%));
  }
  &::after {
    content: '';
    position: absolute;
    aspect-ratio: 1;
    width: calc(var(--size) * 1%);
    animation: border-beam calc(var(--duration) * 1s) cubic-bezier(0.4, 0.14, 0.6, 0.86) infinite;
    background: var(--border-color);
    offset-anchor: calc(var(--anchor) * 1%) 90%;
    offset-path: rect(0 auto auto 0 round calc(var(--size) * 1%));
  }
}

@keyframes border-beam {
  0% {
    offset-distance: -60%;
  }
  50% {
    offset-distance: -10%;
  }
  100% {
    offset-distance: 40%;
  }
}

@keyframes border-beam-reverse {
  0% {
    offset-distance: -10%;
  }
  50% {
    offset-distance: 40%;
  }
  100% {
    offset-distance: 90%;
  }
}
</style>