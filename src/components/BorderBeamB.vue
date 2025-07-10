<template>
  <div
    ref="containerRef"
    class="border-beam-container"
  >
    <!-- 動態生成的 SVG -->
    <svg
      v-if="pathD"
      :width="svgDimensions.width"
      :height="svgDimensions.height"
      class="absolute inset-0"
      :viewBox="`0 0 ${svgDimensions.width} ${svgDimensions.height}`"
      xmlns="http://www.w3.org/2000/svg"
    >
      <!-- 背景路徑 -->
      <path
        :d="pathD"
        :stroke="background"
        :stroke-width="borderWidth"
        fill="none"
        stroke-linecap="round"
      />
      <!-- mask路徑 -->
      <mask id="mask_love">
        <path
          :d="pathD"
          stroke="#ffffff"
          :stroke-width="borderWidth"
          fill="none"
          stroke-linecap="round"
        />
      </mask>
      <g mask="url(#mask_love)" v-if="showBeam">
        <!-- 動畫小點 -->
        <circle
          cx="0"
          cy="0"
          :r="svgDimensions.width * size / 100"
          :fill="borderColor"
        >
          <animateMotion
            keyTimes="0;0.1;0.4;0.5;0.6;0.9;1"
            keyPoints="0;0.05;0.42;0.5;0.55;0.92;1"
            calcMode="linear"
            :dur="`${duration}s`"
            repeatCount="indefinite"
            :path="pathD"
          />
        </circle>
        <circle
          cx="0"
          cy="0"
          :r="svgDimensions.width * size / 100"
          :fill="borderColor"
        >
          <animateMotion
            :begin="`${- duration / 2}s`"
            keyTimes="0;0.1;0.4;0.5;0.6;0.9;1"
            keyPoints="0;0.05;0.42;0.5;0.55;0.92;1"
            calcMode="linear"
            :dur="`${duration}s`"
            repeatCount="indefinite"
            :path="pathD"
          />
        </circle>
      </g>
    </svg>

    <!-- 內容區域 -->
    <div class="relative z-10">
      <slot />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'

// #region Props
interface Props {
  /** 邊框寬度 */
  borderWidth?: number;
  /** 邊框顏色，請使用包含 # 之 HEX 格式 */
  borderColor?: string;
  /** 邊框圓角 */
  borderRadius?: number;
  /** 底色，請使用包含 # 之 HEX 格式 */
  background?: string;
  /** 動畫持續時間 (秒) */
  duration?: number;
  /** 邊框元素大小 (%) */
  size?: number;
  /** 是否顯示beam */
  showBeam?: boolean;
  /** 形狀 'heart', 'rectangle' */
  shape?: string;
}
// #endregion Props

const props = withDefaults(defineProps<Props>(), {
  borderWidth: 2,
  borderColor: '#ffffff',
  borderRadius: 8,
  background: '#555555',
  duration: 2,
  size: 30,
  showBeam: true,
  shape: 'heart'
})

const containerRef = ref<HTMLElement | null>(null)
const pathD = ref('')
const svgDimensions = ref({ width: 0, height: 0 })

// 根據形狀生成路徑
const generatePath = (width:number , height:number , shape:string) => {
  const centerX = width / 2
  const centerY = height / 2
  const radius = Math.min(width, height) / 2 - props.borderWidth

  switch (shape) {
    case 'heart':
      return generateHeartPath(centerX, centerY, radius)
    case 'rectangle':
      const r = props.borderRadius
      return `M ${r},0 L ${width - r},0 Q ${width},0 ${width},${r} L ${width},${height - r} Q ${width},${height} ${width - r},${height} L ${r},${height} Q 0,${height} 0,${height - r} L 0,${r} Q 0,0 ${r},0`
    default:
      return generateHeartPath(centerX, centerY, radius)
  }
}

// 生成愛心路徑
const generateHeartPath = (centerX:number , centerY:number , radius:number ) => {
  const scale = radius / 12 // 基於原始 24x24 viewBox 的比例

  // 使用之前的愛心 SVG 路徑，但調整到容器中心
  const offsetX = centerX - 12 * scale
  const offsetY = centerY - 12 * scale

  const heartPath = `
    M ${12 * scale + offsetX},${21.35 * scale + offsetY}
    L ${(12 - 1.45) * scale + offsetX},${(21.35 - 1.32) * scale + offsetY}
    C ${5.4 * scale + offsetX},${15.36 * scale + offsetY} ${2 * scale + offsetX},${12.28 * scale + offsetY} ${2 * scale + offsetX},${8.5 * scale + offsetY}
    C ${2 * scale + offsetX},${5.42 * scale + offsetY} ${4.42 * scale + offsetX},${3 * scale + offsetY} ${7.5 * scale + offsetX},${3 * scale + offsetY}
    C ${9.24 * scale + offsetX},${3 * scale + offsetY} ${10.91 * scale + offsetX},${3.81 * scale + offsetY} ${12 * scale + offsetX},${5.09 * scale + offsetY}
    C ${13.09 * scale + offsetX},${3.81 * scale + offsetY} ${14.76 * scale + offsetX},${3 * scale + offsetY} ${16.5 * scale + offsetX},${3 * scale + offsetY}
    C ${19.58 * scale + offsetX},${3 * scale + offsetY} ${22 * scale + offsetX},${5.42 * scale + offsetY} ${22 * scale + offsetX},${8.5 * scale + offsetY}
    C ${22 * scale + offsetX},${12.28 * scale + offsetY} ${18.6 * scale + offsetX},${15.36 * scale + offsetY} ${13.45 * scale + offsetX},${20.03 * scale + offsetY}
    L ${12 * scale + offsetX},${21.35 * scale + offsetY}
  `

  return heartPath.replace(/\s+/g, ' ').trim()
}

// 更新路徑
const updatePath = () => {
  if (!containerRef.value) return

  const rect = containerRef.value.getBoundingClientRect()
  const width = rect.width
  const height = rect.height

  svgDimensions.value = { width, height }
  pathD.value = generatePath(width, height, props.shape)
}

// ResizeObserver
let resizeObserver: ResizeObserver | null = null

onMounted(() => {
  nextTick(() => {
    updatePath()

    // 設置 ResizeObserver
    resizeObserver = new ResizeObserver(() => {
      updatePath()
    })

    if (containerRef.value) {
      resizeObserver.observe(containerRef.value)
    }
  })
})

onUnmounted(() => {
  if (resizeObserver) {
    resizeObserver.disconnect()
  }
})

// 監聽 props 變化
watch([() => props.shape, () => props.borderRadius], () => {
  nextTick(() => {
    updatePath()
  })
})
</script>

<style scoped>
.border-beam-container {
  position: relative;
  width: 100%;
}
</style>