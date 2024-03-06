<template>
  <div class="baseInput">
    <div>
      <span>雷數:</span>
      <input
        type="number"
        :min="0"
        :max="computeMax"
        v-model.lazy="modelValue.mines"
        @change="handleInputChange('mines', computeMax, 0)"
      />
    </div>
    <div>
      <span>欄數:</span>
      <input
        type="number"
        :min="1"
        :max="15"
        v-model.lazy="modelValue.row"
        @change="handleInputChange('row', 15)"
      />
    </div>
    <div>
      <span>排數:</span>
      <input
        type="number"
        :min="1"
        :max="30"
        v-model.lazy="modelValue.col"
        @change="handleInputChange('col', 30)"
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
  <p>{{ modelValue }}</p>
</template>

<script lang="ts" setup>
import { computed, defineEmits, reactive } from "vue";
import { MyData } from "./modal/InputData.ts";
const modelValue = reactive<MyData>({ mines: 10, row: 10, col: 10 });

const computeMax = computed(() => {
  let tempMines = (modelValue.row ?? 10) * (modelValue.col ?? 10);
  let result = Math.floor((tempMines * 4) / 5);
  modelValue.mines = 0;
  return result;
});
const emits = defineEmits(["update"]);
function handleInputChange(property: string, max: number, min: number = 1) {
  let newValue = modelValue[property as keyof MyData] ?? NaN;
  if (isNaN(parseInt(newValue.toString())) || newValue <= min) {
    newValue = min;
  } else {
    newValue = newValue > max ? max : newValue;
  }
  modelValue[property as keyof MyData] = newValue;
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
