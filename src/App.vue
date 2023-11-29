<template>
  <main font-sans p="x-4 y-10" text="center gray-700" class="app">
    <RouterView />
    <TheFooter />
  </main>
</template>

<style>
.app {
  background: linear-gradient(to right, #FF1493, #FFFF00);

  height: 100vh;
}
</style>
