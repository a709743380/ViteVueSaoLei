<template>
  <div class="baseInput">
    <div>
      <lable>雷數:</lable>
      <input
        type="number"
        v-model="modelValue.mines"
        @input="handleInput($event, 'mines', computeMax, computeMax / 5)"
        :min="computeMax / 5"
        :max="computeMax"
      />
    </div>
    <div>
      <lable>欄數:</lable>
      <input
        type="number"
        :min="1"
        :max="15"
        @input="handleInput($event, 'row', 15)"
        v-model="modelValue.row"
      />
    </div>
    <div>
      <lable>排數:</lable>
      <input
        type="number"
        :min="1"
        :max="30"
        v-model="modelValue.col"
        @input="handleInput($event, 'col', 30)"
      />
    </div>
  </div>

  <button
    @click="
      () => {
        emitValue();
      }
    "
    class="btn"
  >
    設定遊戲數值
  </button>
</template>

<script lang="ts" setup>
import { computed, defineEmits, reactive } from "vue";
import { MyData } from "./modal/InputData.ts";
const modelValue = reactive<MyData>({ mines: 10, row: 10, col: 10 });

const computeMax = computed(() => {
  let tempMines = (modelValue.row ?? 10) * (modelValue.col ?? 10);
  let result = tempMines - Math.ceil((tempMines * 4) / 5);
  modelValue.mines = result;
  return result;
});
const emits = defineEmits(["update"]);
function handleInput(e: any, property: string, max: number, min: number = 1) {
  let mincheck = e.target.value <= 0;
  if (mincheck) {
    if (property == "row") {
      modelValue.row = min;
    } else if (property == "col") {
      modelValue.col = min;
    } else if (property == "mines") {
      modelValue.mines = min;
    }
  } else {
    let check = e.target.value > max;
    if (property == "row") {
      modelValue.row = check ? max : e.target.value;
    } else if (property == "col") {
      modelValue.col = check ? max : e.target.value;
    } else if (property == "mines") {
      modelValue.mines = check ? max : e.target.value;
    }
  }
}
function emitValue() {
  emits("update", modelValue);
}
</script>
<style scoped>
.baseInput {
  display: flex;
  flex-direction: column;
  justify-items: center;
}
.btn {
  background: skyblue;
}
input {
  width: 200px;
  height: 25px;
  font-size: 1.2rem;
}
</style>
