<script setup lang="ts">
interface Shape {
  shape: string
  yPosition: number
  xPosition: number
  rotationLevel: number
  hasLanded: boolean
  // TODO: handle items that are bigger than one column
  verticalSpace?: number
  horizontalSpace?: number
}
const numOfYCells = 9
const numOfXCells = 9
const cells = Array.from({ length: numOfYCells }).fill(Array.from({ length: numOfXCells }).fill(0))
const arena = ref(cells)
const stack = ref<Shape[]>([])

function onDrop(event: DragEvent) {
  const shape = event.dataTransfer?.getData('shape')
  if (!shape) {
    // eslint-disable-next-line no-alert
    alert('Not a valid shape')
    return
  }
  // TODO: handle adding shape when last item has not landed

  // Adding the shape to the center top.
  stack.value.push({ shape, xPosition: 4, yPosition: 0, rotationLevel: 0, hasLanded: false })
}

function getShapeInColumn(xPos: number, yPos: number) {
  return stack.value.find(item => item.xPosition === xPos && item.yPosition === yPos)
}

function changeItemHorizontalPosition(next: 1 | -1) {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]
  if ((element.xPosition === 0 && next < 0) || (element.xPosition >= numOfXCells - 1 && next > 0) || element.hasLanded)
    return

  element.xPosition += next
}

let interval: NodeJS.Timer | undefined
/**
 * Handle down movement
 * */
function landLastShape() {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]
  const itemInNextColumnExists = !!getShapeInColumn(element?.xPosition, element?.yPosition + 1)

  if (itemInNextColumnExists)
    stack.value[elementIndex + 1].hasLanded = true
  else stack.value[elementIndex].yPosition += 1

  if (stack.value[elementIndex].yPosition === numOfYCells - 1 || stack.value[elementIndex].hasLanded) {
    clearInterval(interval)
    stack.value[elementIndex].hasLanded = true
  }
}
watch(() => stack.value[stack.value.length - 1], (source) => {
  if (!source.hasLanded) {
    // Set the interval initially
    interval = setInterval(landLastShape, 1000)
  }
})

function changeRotation(nextRotation: 1 | -1) {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]

  if (element.hasLanded || element.rotationLevel > 3)
    return

  stack.value[elementIndex].rotationLevel += nextRotation
}

function initKeyboardControls() {
  document.addEventListener('keydown', (event) => {
    if (event.code === 'ArrowLeft')
      changeItemHorizontalPosition(-1)

    else if (event.code === 'ArrowRight')
      changeItemHorizontalPosition(1)
    else if (event.code === 'ArrowUp')
      changeRotation(1)
    else if (event.code === 'ArrowDown')
      changeRotation(-1)
  })
}
onMounted(() => {
  initKeyboardControls()
})
</script>

<template>
  <div id="board" class="board grid bg-gray-50" @drop="onDrop" @dragover.prevent @dragenter.prevent>
    <div v-for="(row, yIndex) in arena" :key="yIndex" class="grid grid-cols-9">
      <div v-for="(_, xIndex) in row" :key="xIndex" class="flex items-center justify-center gap-4 border">
        <div v-if="getShapeInColumn(xIndex, yIndex)" :class="getShapeInColumn(xIndex, yIndex)?.shape" />
      </div>
    </div>
  </div>
</template>
