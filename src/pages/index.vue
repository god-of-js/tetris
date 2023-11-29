<script setup lang="ts" generic="T extends any, O extends any">
defineOptions({
  name: 'IndexPage',
})
const shapes = ref(['triangle', 'rectangle', 'square'])
const boardKey = ref(0)

function refreshBoard() {
  boardKey.value += 1
}
</script>

<template>
  <div class="mx-auto w-fit flex flex-col gap-8 md:flex-row">
    <TetrisShapes :shapes="shapes" @refresh-board="refreshBoard" />
    <div>
      <TetrisBoard :key="boardKey" :shapes="shapes" />

      <a
        i-carbon-logo-github icon-btn
        rel="noreferrer"
        href="https://github.com/god-of-js/tetris"
        target="_blank"
        title="GitHub"
        class="mx-auto mt-4 text-xl"
      />
    </div>
  </div>
</template>

<style>
.triangle {
  border-bottom: 60px solid #00FFFF;
  border-right: 60px solid transparent;
  width: 0;
  height: 0;
}

.rectangle {
  height: 120px;
  width: 60px;
  background: linear-gradient(to right, #00FFFF, #FFD700);

}

.square {
  width: 60px;
  height: 60px;
  background: linear-gradient(to right, #FFD700, #FF4500);
}
</style>
