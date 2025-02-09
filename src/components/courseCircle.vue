<template>
  <div class="bContainer">
    <button id="btn" @click="addCircle">+</button>
    <br />
    <input
      id="inputName"
      type="text"
      v-model="cName"
      placeholder="course name"
      required
    />
  </div>
  <div id="container">
    <div
      v-for="(item, index) in items"
      :key="index"
      class="draggable"
      ref="draggable"
      @mousedown="startDrag($event, index)"
      :style="{ left: item.x + 'px', top: item.y + 'px' }"
    >
      <IconCircle />
      <p id="nameDisplay">{{ item.cName }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from "vue";
import IconCircle from "./icons/IconCircle.vue";
var items = ref([]);

const offset = ref({ x: 0, y: 0, index: null });

const addCircle = () => {
  if (document.getElementById("inputName").value == "") {
    alert("Please enter a course name");
    return;
  }
  const button = document.getElementById("btn").getBoundingClientRect();

  items.value.push({
    x: button.x + button.width,
    y: button.y - button.height / 3,
    dragging: false,
    cName: document.getElementById("inputName").value,
  });
  document.getElementById("inputName").value == "";
};

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

#btn {
  background-color: rgb(94, 80, 80);
  border: none;
  color: white;
  padding: 10px;
  margin-left: 50px;
  margin-bottom: 20px;
  display: inline-block;
  font-size: 40px;
  border-radius: 1rem;
  cursor: pointer;
}

.bContainer {
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  padding-top: 23vw;
  padding-left: 3vw;
  width: 100vw;
  height: 100vw;
}

#container {
  position: fixed;
  top: 0;
  left: 0;
  background: #00000000;
}

.draggable {
  position: absolute;
  cursor: grab;
  text-align: center;
  user-select: none;
  font-weight: bold;
}

#inputName {
  height: 35px;
  width: 180px;
  font-size: 20px;
  border: none;
  border-bottom: 2px solid red;
  border-radius: 4px;
  background-color: gray;
}

#nameDisplay {
  font-size: 17px;
  color: white;
}
</style>
