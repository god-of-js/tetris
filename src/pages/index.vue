<script setup lang="ts" generic="T extends any, O extends any">
import TheButton from '~/components/TheButton.vue'

defineOptions({
  name: 'IndexPage',
})

const shapes = ref(['triangle', 'rectangle', 'square'])

function onDrop(event: DragEvent) {
  const shape = event.dataTransfer?.getData('shape')
  alert(shape)
}

function startDrag(event: DragEvent, shape: string) {
  if (!event.dataTransfer)
    return
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.setData('shape', shape)
}
</script>

<template>
  <div class="mx-auto mb-20 md:w-65%">
    <div class="mb-8 flex items-center gap-2">
      <div i-carbon-campsite text-xl />
      <span>Tetris</span>
    </div>
    <div class="mx-auto flex flex-col gap-8 md:flex-row">
      <div class="grid h-fit gap-4 align-start">
        <TheButton class="h-fit">
          Clear
        </TheButton>
        <div v-for="shape in shapes" :key="shape" :class="shape" draggable="true" @dragstart="startDrag($event, shape)" />
      </div>
      <div id="board" class="board flex items-end justify-center bg-gray-50" @drop="onDrop" @dragover.prevent @dragenter.prevent>
        <div class="triangle" />
      </div>
    </div>
  </div>
</template>

<style>
.triangle {
  border-bottom: 30px solid blue;
  border-right: 30px solid transparent;
  width: 0;
  height: 0;
}

.rectangle {
  height: 60px;
  width: 30px;
  background: red;
}

.square {
  width: 30px;
  height: 30px;
  background: yellow;
}
.board {
  width: 600px;
  height: 400px;
}
</style>
