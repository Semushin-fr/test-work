<template>
  <div class="container">
    <form class="form">
      <label>Радиус окружности:</label>
      <input type="number" v-model="circleRadius" @input="updateCanvas" />
      <br />
      <label>Размер квадрата:</label>
      <input type="number" v-model="squareSize" @input="updateCanvas" />
    </form>

    <div class="canvas">
      <canvas ref="canvas" @mousedown="handleMouseDown" @mousemove="handleMouseMove" @mouseup="handleMouseUp" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
const canvasSize = 400
const canvas = ref(null)
const ctx = ref(null)

const maxCircleRadius = 150
const minCircleRadius = 50
const maxSquareSize = 200
const minSquareSize = 50
let circleRadius = 150
let squareSize = 50
let squareX = null
let squareY = null
let isDragging = false
let dragOffsetX = null
let dragOffsetY = null

const draw = () => {
  ctx.value.clearRect(0, 0, canvas.value.width, canvas.value.height)

  ctx.value.beginPath()
  ctx.value.arc(
    canvas.value.width / 2,
    canvas.value.height / 2,
    circleRadius,
    0,
    2 * Math.PI
  )
  ctx.value.fillStyle = 'green';
  ctx.value.fill();

  ctx.value.fillStyle = 'yellow';
  ctx.value.fillRect(squareX, squareY, squareSize, squareSize);
};

const updateCanvas = () => {
  if (circleRadius > maxCircleRadius) {
    circleRadius = maxCircleRadius
  }

  if (circleRadius < minCircleRadius) {
    circleRadius = minCircleRadius
  }

  if (squareSize > maxSquareSize) {
    squareSize = maxSquareSize
  }

  if (squareSize < minSquareSize) {
    squareSize = minSquareSize
  }

  squareX = canvas.value.width / 2 - squareSize / 2;
  squareY = canvas.value.height / 2 - squareSize / 2;
  draw();
};

const handleMouseDown = (event) => {
  const rect = canvas.value.getBoundingClientRect()
  const mouseX = event.clientX - rect.left
  const mouseY = event.clientY - rect.top

  if (
    mouseX >= squareX &&
    mouseX <= squareX + squareSize &&
    mouseY >= squareY &&
    mouseY <= squareY + squareSize
  ) {
    isDragging = true
    dragOffsetX = mouseX - squareX
    dragOffsetY = mouseY - squareY
  }
};

const handleMouseMove = (event) => {
  if (isDragging) {
    const rect = canvas.value.getBoundingClientRect();
    const mouseX = event.clientX - rect.left;
    const mouseY = event.clientY - rect.top;

    const distance = Math.sqrt(
      Math.pow(mouseX - canvas.value.width / 2, 2) +
      Math.pow(mouseY - canvas.value.height / 2, 2)
    );

    if (distance <= circleRadius) {
      squareX = mouseX - dragOffsetX
      squareY = mouseY - dragOffsetY
      draw()
    }
  }
};

const handleMouseUp = () => {
  isDragging = false
};

onMounted(() => {
  const canvasElement = canvas.value
  canvasElement.width = canvasSize
  canvasElement.height = canvasSize
  squareX = canvasElement.width / 2 - squareSize / 2
  squareY = canvasElement.height / 2 - squareSize / 2

  ctx.value = canvasElement.getContext('2d')
  draw()
})
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  height: 100vh;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
}

.form {
  margin-bottom: 20px;
}
</style>