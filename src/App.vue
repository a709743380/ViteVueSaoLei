<template>
  {{ myData }}
  <HelloWorld :modelValue="myData" :key="componentKey" />
  <InputData @update="setMines" />
</template>

<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
import InputData from "./components/InputData.vue";
import { reactive, ref } from "vue";
import { MyData } from "./components/modal/InputData.ts";
import CryptoJS from "crypto-js";

const myData = reactive<MyData>({});
const componentKey = ref("");

function setMines(data: MyData) {
  Object.assign(myData, data);
  let key = JSON.stringify(data);
  componentKey.value = hashString(key);
}

function hashString(input: string) {
  const hash = CryptoJS.SHA256(input).toString();
  return hash;
}
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
