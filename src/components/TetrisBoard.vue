<script setup lang="ts">
interface Props {
  shapes: string[]
}
interface Shape {
  shape: string
  yPosition: number
  xPosition: number
  rotationLevel: number
  hasLanded: boolean
  verticalSpace: number
}

const props = defineProps<Props>()
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
  if (!shape || !props.shapes.includes(shape)) {
    // eslint-disable-next-line no-alert
    alert('Not a valid shape')
    return
  }
  if (!!stack.value[stack.value.length - 1] && !stack.value[stack.value.length - 1]?.hasLanded) {
    // eslint-disable-next-line no-alert
    alert('The last item needs to land before we proceed')
    return
  }
  let yPosition = yIndex
  if (shape === 'rectangle' && yIndex === 0)
    yPosition = 1

  stack.value.push({
    shape,
    xPosition: xIndex,
    yPosition,
    rotationLevel: 0,
    hasLanded: false,
    verticalSpace: shape === 'rectangle' ? 2 : 1,
  })

  if (prevYIndex && prevXIndex)
    removeShapeFromStack(Number(prevXIndex), Number(prevYIndex))
}

function removeShapeFromStack(xIndex: number, yIndex: number) {
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
function moveItemOneStepDown() {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]
  const itemInNextColumnExists = !!getShapeInColumn(element?.xPosition, element?.yPosition + 1)
  const elementHasReachedItsEnd = element.yPosition >= numOfYCells - 1
  const itemTwoColumnsBelow = getShapeInColumn(element?.xPosition, element?.yPosition + 2)
  const itemAtRightBelow = getShapeInColumn(element?.xPosition + 1, element?.yPosition + 1)
  const itemAtRightBelowIsDoubleSizedAndRotated
  = itemAtRightBelow
    ? itemAtRightBelow.verticalSpace === 2 && ![0, 2].includes(itemAtRightBelow.rotationLevel)
    : false
  const itemTwoColumnsBelowIsDoubleSized = itemTwoColumnsBelow?.verticalSpace === 2 && itemTwoColumnsBelow.rotationLevel === 0

  if (
    elementHasReachedItsEnd
    || itemInNextColumnExists
    || itemTwoColumnsBelowIsDoubleSized
    || element.hasLanded
    || itemAtRightBelowIsDoubleSizedAndRotated) {
    clearInterval(interval)
    element.hasLanded = true
  }
  else { element.yPosition += 1 }
}

watch(() => stack.value[stack.value.length - 1], (source) => {
  if (!source.hasLanded)
    interval = setInterval(moveItemOneStepDown, 1000)
})

function changeRotation(nextRotation: 1 | -1) {
  const elementIndex = stack.value.length - 1
  const element = stack.value[elementIndex]

  if (element.hasLanded)
    return

  const elementIsDoublesizedAndAtTheEdge = (element.xPosition === 0) && element.verticalSpace === 2 && element.rotationLevel !== 0
  if (elementIsDoublesizedAndAtTheEdge)
    element.xPosition += 1

  if (element.verticalSpace === 2) {
    element.rotationLevel = element.rotationLevel === 0 ? 1 : 0
    return
  }

  if (element.rotationLevel >= 3 && nextRotation === 1) {
    // Restart rotation if it has reached it's end
    element.rotationLevel = 0
  }
  else if (element.rotationLevel === 0 && nextRotation === -1) {
    element.rotationLevel = 3
  }
  else {
    element.rotationLevel += nextRotation
  }
}

function initKeyboardControls() {
  document.addEventListener('keydown', (event) => {
    if (!stack.value[stack.value.length - 1] || stack.value[stack.value.length - 1].hasLanded)
      return
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
  <div class="grid bg-gray-50">
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
          :class="[getShapeInColumn(xIndex, yIndex)?.shape,
                   {
                     'negative-half-margin':
                       getShapeInColumn(xIndex, yIndex)?.verticalSpace === 2
                       && getShapeInColumn(xIndex, yIndex)?.rotationLevel !== 0,
                   },
          ]"
          :style="`transform: rotate(${getShapeInColumn(xIndex, yIndex)!.rotationLevel * 90}deg);`"
          @dragstart="startDrag($event, getShapeInColumn(xIndex, yIndex)?.shape!, xIndex, yIndex)"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.shape-cell {
  width: 60px;
  height: 60px;
  display: flex;
  align-items: flex-end;
  justify-content: flex-start;
}
.negative-half-margin {
  margin-bottom: -30px;
  margin-left: -30px;
}
</style>
