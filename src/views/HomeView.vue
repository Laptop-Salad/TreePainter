<script setup>
import {onMounted, ref, watch} from "vue";
import Node from "@/objects/Node.js";
import TpInput from "../components/TPInput.vue";

let canvas;
let ctx;
let currentX;
let currentY;
let nodeArray = ref(['R', 'A', 'B', 'C', 'D', 'E', 'F', null, null, null, null, null, null, 'G']);

let userArray = ref('');

onMounted(() => {
  canvas = document.getElementById("canvas");
  ctx = canvas.getContext("2d");

  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  currentX = window.innerWidth / 2;
  currentY = 100;

  ctx.font = '16px Arial';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';

  drawTree();
})

function drawTree() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const root = insert(0);
  traverse(root, currentX, currentY, 200);
}

function drawNode(value, x, y) {
  if (value !== null) {
    ctx.beginPath();
    ctx.arc(x, y, 40, 0, 2 * Math.PI);
    ctx.stroke();

    ctx.fillText(value, x, y);
  }
}

function insert(i) {
  let root = null;

  if (i < nodeArray.value.length) {
    root = new Node(nodeArray.value[i]);

    root.right = insert(2 * i + 1);
    root.left = insert(2 * i + 2);
  }

  return root;
}

function traverse(root, x, y, offset) {
  if (root != null && root.value != null) {
    drawNode(root.value, x, y);

    traverse(root.right, x - offset, y + 120, offset / 2);
    traverse(root.left, x + offset, y + 120, offset / 2);
  }
}

function handleUserArrChange() {
  try {
    let array = userArray.value.split(",");

    array.forEach((element, index) => {
      array[index] = element.replace(/ /g,'');

      if (array[index] === 'null') {
        array[index] = null;
      }
    })

    nodeArray.value = array;

    drawTree();
  } catch (e) {
    console.log(e);
  }
}

watch(userArray, async () => {
  handleUserArrChange();
});
</script>

<template>
  <div class="flex justify-center items-center">
    <canvas id="canvas" class="bg-amber-200"></canvas>

    <div class="absolute self-end p-5 m-4 w-1/2">
      <div class="bg-white w-full rounded-md p-5">
        <TpInput
            id="array"
            label="Array"
            placeholder="R, A, B, C, D, E, F, null, null, null, null, null, null, 'G'"
            v-model="userArray"
        />
      </div>
    </div>
  </div>
</template>