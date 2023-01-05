<template>
  <div>
    Tool:
    <select id="tool" @change="changeTool('brush')">
      <option value="brush">Brush</option>
      <option value="eraser">Eraser</option>
    </select>
    <div id="container"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import Konva from "konva";

const canvas = ref();
const width = ref(window.innerWidth);
const height = ref(window.innerHeight - 25);
const isPaint = ref<boolean>(false);
const mode = ref<string>("brush");
const lastLine = ref();
const layer = ref();

onMounted(() => {
  createCanvas();
  createLayer();
  brushMode();
});

const createCanvas = () => {
  canvas.value = new Konva.Stage({
    container: "container",
    width: width.value,
    height: height.value,
  });
};

const createLayer = () => {
  layer.value = new Konva.Layer();
  canvas.value.add(layer.value);
};

const brushMode = () => {
  canvas.value.on("mousedown touchstart", (e) => {
    isPaint.value = true;
    var pos = canvas.value.getPointerPosition();
    lastLine.value = new Konva.Line({
      stroke: "#df4b26",
      strokeWidth: 5,
      globalCompositeOperation:
        mode.value === "brush" ? "source-over" : "destination-out",
      // round cap for smoother lines
      lineCap: "round",
      lineJoin: "round",
      // add point twice, so we have some drawings even on a simple click
      points: [pos.x, pos.y, pos.x, pos.y],
    });
    layer.value.add(lastLine.value);
  });

  canvas.value.on("mouseup touchend", function () {
    isPaint.value = false;
  });

  canvas.value.on("mousemove touchmove", function (e) {
    if (!isPaint) {
      return;
    }

    // prevent scrolling on touch devices
    e.evt.preventDefault();

    const pos = canvas.value.getPointerPosition();
    var newPoints = lastLine.value.points().concat([pos.x, pos.y]);
    lastLine.value.points(newPoints);
  });
};

const changeTool = (value) => {
  console.log(value);
  mode.value = value;
};
</script>

<style scoped></style>
