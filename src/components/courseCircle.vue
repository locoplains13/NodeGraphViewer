<template>
  <div class="container">
    <div
      v-for="(item, index) in items"
      :key="index"
      class="draggable"
      ref="draggable"
      @mousedown="startDrag($event, index)"
      :style="{ left: item.x + 'px', top: item.y + 'px' }"
    >
      <IconCircle />
      <p>course</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from "vue";
import IconCircle from "./icons/IconCircle.vue";

const items = ref([
  { x: 100, y: 100, dragging: false },
  { x: 300, y: 200, dragging: false },
]);

const offset = ref({ x: 0, y: 0, index: null });

const startDrag = (event, index) => {
  offset.value.x = event.clientX - items.value[index].x;
  offset.value.y = event.clientY - items.value[index].y;
  offset.value.index = index;
  items.value[index].dragging = true;

  document.addEventListener("mousemove", onDrag);
  document.addEventListener("mouseup", stopDrag);
};

const onDrag = (event) => {
  if (offset.value.index !== null) {
    const index = offset.value.index;
    items.value[index].x = event.clientX - offset.value.x;
    items.value[index].y = event.clientY - offset.value.y;
  }
};

const stopDrag = () => {
  if (offset.value.index !== null) {
    items.value[offset.value.index].dragging = false;
    offset.value.index = null;
  }

  document.removeEventListener("mousemove", onDrag);
  document.removeEventListener("mouseup", stopDrag);
};

// Cleanup event listeners when component unmounts
onUnmounted(() => {
  document.removeEventListener("mousemove", onDrag);
  document.removeEventListener("mouseup", stopDrag);
});
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: #000000;
}

.draggable {
  position: absolute;
  cursor: grab;
  text-align: center;
  user-select: none;
  font-weight: bold;
}
</style>
