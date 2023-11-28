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

function startDrag(event: DragEvent, shape: string, xIndex: number, yIndex: number) {
  if (!event.dataTransfer)
    return
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.setData('shape', shape)
  event.dataTransfer.setData('xIndex', `${xIndex}`)
  event.dataTransfer.setData('yIndex', `${yIndex}`)
}
function receiveShape(event: DragEvent, yIndex: number, xIndex: number) {
  const shape = event.dataTransfer?.getData('shape')
  const prevXIndex = event.dataTransfer?.getData('xIndex')
  const prevYIndex = event.dataTransfer?.getData('yIndex')
  if (!shape) {
    // eslint-disable-next-line no-alert
    alert('Not a valid shape')
    return
  }
  // TODO: handle adding shape when last item has not landed

  stack.value.push({ shape, xPosition: xIndex, yPosition: yIndex, rotationLevel: 0, hasLanded: false })

  if (prevYIndex && prevXIndex)
    removeShape(Number(prevXIndex), Number(prevYIndex))
}

function removeShape(xIndex: number, yIndex: number) {
  stack.value = stack.value.filter(item => !(item.xPosition === xIndex && item.yPosition === yIndex))
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
function moveItemOneStepDown() {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]
  const itemInNextColumnExists = !!getShapeInColumn(element?.xPosition, element?.yPosition + 1)

  if (itemInNextColumnExists)
    element.hasLanded = true
  else element.yPosition += 1

  if (element.yPosition === numOfYCells - 1 || element.hasLanded) {
    clearInterval(interval)
    element.hasLanded = true
  }
}

watch(() => stack.value[stack.value.length - 1], (source) => {
  if (!source.hasLanded) {
    // Set the interval initially
    interval = setInterval(moveItemOneStepDown, 1000)
  }
})

function changeRotation(nextRotation: 1 | -1) {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]

  if (element.hasLanded)
    return

  if (element.rotationLevel >= 3 && nextRotation === 1)
    element.rotationLevel = 0
  else if (element.rotationLevel === 0 && nextRotation === -1)
    element.rotationLevel = 3
  else
    element.rotationLevel += nextRotation
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
  <div id="board" class="board grid bg-gray-50">
    <div v-for="(row, yIndex) in arena" :key="yIndex" class="grid grid-cols-9">
      <div
        v-for="(_, xIndex) in row"
        :key="xIndex" class="shape-cell flex items-center justify-center gap-4 border"
        @drop="(e) => receiveShape(e, yIndex, xIndex)"
        @dragover.prevent
        @dragenter.prevent
      >
        <div
          v-if="getShapeInColumn(xIndex, yIndex)"
          draggable="true"
          :class="getShapeInColumn(xIndex, yIndex)?.shape"
          :style="`transform: rotate(${getShapeInColumn(xIndex, yIndex)!.rotationLevel * 90}deg);`"
          @dragstart="startDrag($event, getShapeInColumn(xIndex, yIndex)?.shape!, xIndex, yIndex)"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.shape-cell {
  width: 30px;
  height: 30px;
}
</style>
