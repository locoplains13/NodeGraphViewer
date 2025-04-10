<template>
  <div id="container">
    <!-- Instructions for the user -->
    <div id="instructions">
      <p>Click button on the left to add node</p>
      <p>
        -You can drag it around
        <br />
        -Hold ctrl and click on two nodes to connect them
        <br />
        -Hold shift and click on a node to delete it
      </p>
    </div>
    <!-- Popup for displaying messages -->
    <div id="popup"><p id="popup-text"></p></div>
    <!-- Loop through lines and draw them -->
    <div
      v-for="(line, index) in lines"
      :key="index"
      :style="{ stroke: 'white', 'stroke-width': 2 }"
    >
      <svg
        id="line"
        height="2000"
        width="3000"
        xmlns="http://www.w3.org/2000/svg"
      >
        <line
          :x1="items[line.index1].x + 50"
          :y1="items[line.index1].y + 50"
          :x2="items[line.index2].x + 50"
          :y2="items[line.index2].y + 50"
        />
      </svg>
    </div>
    <!-- Button to add a new circle -->
    <div class="bContainer">
      <button id="btn" @click="addCircle()">+</button>
      <br />
      <!-- Input for the name of the new circle -->
      <input
        id="inputName"
        type="text"
        v-model="cName"
        placeholder="node name"
        required
      />
    </div>
    <!-- Loop through items and display them as draggable circles -->
    <div
      v-for="(item, index) in items"
      :key="item.id"
      class="draggable"
      ref="draggable"
      v-on:click.ctrl="handleConnectLine(index)"
      v-on:click.shift="handleDelete(index)"
      @mousedown="startDrag($event, index)"
      :style="{ left: item.x + 'px', top: item.y + 'px' }"
    >
      <IconCircle />
      <p id="nameDisplay">{{ item.cName }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted, watch } from "vue";
import IconCircle from "./icons/IconCircle.vue";

// Reactive references for items, lines, and dragging offset
const items = ref([]);
const lines = ref([]);
const offset = ref({ x: 0, y: 0, index: null });
const firstSelectedCircle = ref(null);
const secondSelectedCircle = ref(null);

// Add an initial item
items.value.push({ x: 900, y: 400, dragging: false, cName: "Course 1", id: 0 });

var countId = 1;

// Function to add a new circle
const addCircle = () => {
  if (document.getElementById("inputName").value === "") {
    document.getElementById("popup-text").innerHTML =
      "Please enter a course name";
    document.getElementById("popup").style.visibility = "visible";
    return;
  }
  const button = document.getElementById("btn").getBoundingClientRect();

  items.value.push({
    x: button.x + button.width,
    y: button.y - button.height / 3,
    dragging: false,
    cName: document.getElementById("inputName").value,
    id: countId,
  });
  countId++;
  document.getElementById("inputName").value = "";
};

// Function to start dragging a circle
const startDrag = (event, index) => {
  offset.value.x = event.clientX - items.value[index].x;
  offset.value.y = event.clientY - items.value[index].y;
  offset.value.index = index;
  items.value[index].dragging = true;

  document.addEventListener("mousemove", onDrag);
  document.addEventListener("mouseup", stopDrag);
};

// Function to handle dragging a circle
const onDrag = (event) => {
  if (offset.value.index !== null) {
    const index = offset.value.index;
    items.value[index].x = event.clientX - offset.value.x;
    items.value[index].y = event.clientY - offset.value.y;
  }
};

// Function to stop dragging a circle
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

// Function to handle connecting two circles with a line
const handleConnectLine = (index) => {
  if (firstSelectedCircle.value === null) {
    firstSelectedCircle.value = index;
    document.getElementById(
      "popup-text"
    ).innerHTML = `Node <em>${items.value[index].cName}</em> selected`;
    document.getElementById("popup").style.visibility = "visible";
    return;
  } else if (
    secondSelectedCircle.value === null &&
    firstSelectedCircle.value !== index
  ) {
    secondSelectedCircle.value = index;
  }
  if (
    secondSelectedCircle.value !== null &&
    firstSelectedCircle.value !== null
  ) {
    lines.value.push({
      index1: firstSelectedCircle.value,
      index2: secondSelectedCircle.value,
    });
    firstSelectedCircle.value = null;
    secondSelectedCircle.value = null;
    document.getElementById("popup").style.visibility = "hidden";
  }
};

// Function to handle deleting a circle
const handleDelete = (index) => {
  items.value.splice(index, 1);
  lines.value = lines.value.filter(
    (line) => line.index1 !== index && line.index2 !== index
  );
  // Update line indices
  lines.value.forEach((line) => {
    if (line.index1 > index) line.index1--;
    if (line.index2 > index) line.index2--;
  });
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

#btn {
  background-color: rgba(94, 80, 80, 0);
  border: 2px solid #988a89;
  color: #988a89;
  padding: 10px;
  margin-left: 50px;
  margin-bottom: 20px;
  display: inline-block;
  font-size: 40px;
  border-radius: 1rem;
  cursor: pointer;
  transition-duration: 0.4s;
}

#btn:hover {
  background-color: #988a89;
  color: white;
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
  background-color: #ffffff00;
}

#container {
  position: fixed;
  top: 0;
  left: 0;
  background-color: #ffffff00;
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
  margin-top: -25px;
  font-size: 17px;
  color: white;
}
#line {
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
}

#instructions {
  position: fixed;
  top: 0;
  left: 0;
  align-items: center;
  background-color: #ffffff00;
  color: white;
  font-size: 20px;
  font-weight: bold;
}
#popup {
  visibility: hidden;
  font-size: 30px;
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  align-items: center;
  background-color: black;
  border-radius: 10px;
  opacity: 0.5;
  transition-duration: 1s;
}
</style>
