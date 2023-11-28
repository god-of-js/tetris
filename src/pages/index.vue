<script setup lang="ts" generic="T extends any, O extends any">
defineOptions({
  name: 'IndexPage',
})

const shapes = ref(['triangle', 'rectangle', 'square'])
</script>

<template>
  <div class="mx-auto mb-20 md:w-65%">
    <div class="mb-8 flex items-center gap-2">
      <div i-carbon-campsite text-xl />
      <span>Tetris</span>
    </div>
    <div class="mx-auto flex flex-col gap-8 md:flex-row">
      <TetrisShapes :shapes="shapes" />
      <TetrisBoard />
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
</style>
