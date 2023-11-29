<script setup lang="ts">
interface Props {
  shapes: string[]
}
const props = defineProps<Props>()
const emit = defineEmits<{ (e: 'refresh-board'): void }>()

function startDrag(event: DragEvent, shape: string) {
  if (!event.dataTransfer)
    return
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.setData('shape', shape)
}
</script>

<template>
  <div class="grid h-fit gap-4 align-start">
    <TheButton class="h-fit" @click="emit('refresh-board')">
      Clear
    </TheButton>
    <div v-for="shape in props.shapes" :key="shape" :class="shape" draggable="true" @dragstart="startDrag($event, shape)" />
  </div>
</template>
