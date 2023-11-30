<script setup lang="ts">

</script>

<template>
  <div class="h-fit w-96 rounded-lg bg-white p-2 text-left">
    <h1 class="colored-text text-center">
      Shape Tetris
    </h1>
    <p class="text-sm">
      Want to build a castle? A gun or a space UFO? Play with these shapes and test your creativity.
    </p>

    <h3 class="colored-text mt-4 text-sm">
      How to Play
    </h3>

    <ul class="list-disc p-4">
      <li>Drag a shape into any column of choice</li>
      <li>Use the Left  and Right directions on your keyboard to change horizontal columns</li>
      <li>Use Up and Down directions on your keyboard to rotate the shape</li>
      <li>If you are not comforrtable with the position of the shape when it has landed, drag it to any other column of choice and manipulate again</li>
    </ul>

    <div class="colored-text">
      Goodluck Builder!! &#127881; &#127881; &#127881;
    </div>
  </div>
</template>

<style scoped>
.colored-text {
  font-family: 'Helvetica Neue';
  font-style: normal;
  font-weight: 700;
  font-size: 24px;
  background: linear-gradient(to right, #FF1493, #FFFF00);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
</style>
