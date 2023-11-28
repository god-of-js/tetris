<script setup lang="ts">
const shapes = ref(['triangle', 'rectangle', 'square'])

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
    <TheButton class="h-fit">
      Clear
    </TheButton>
    <div v-for="shape in shapes" :key="shape" :class="shape" draggable="true" @dragstart="startDrag($event, shape)" />
  </div>
</template>
