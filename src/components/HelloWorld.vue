<template>
  <div class="bordbase">
    <div style="display: flex" v-for="i in initrow" :key="i">
      <button
        :class="{
          isMine: false,
          isSave: true,
          isOpen: false,
          isNextMines: false,
        }"
        v-for="j in initcol"
        :key="`${i}${j}`"
        @click="
          () => {
            CheckMines(i, j);
          }
        "
        :ref="(el) => setButtonRef(el, i, j)"
      ></button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import JSConfetti from "js-confetti";

const initrow = ref(10);
const initcol = ref(10);
const buttonRefs = ref<HTMLButtonElement[][]>([]);
const miness = ref(10);
const confetti = new JSConfetti();

//遊戲狀態
const gameStaus = ref(0);
function CheckMines(row: number, col: number) {
  // console.log(buttonRefs);
  if (gameStaus.value == 0) {
    setMines(row, col);
  }
  gameStaus.value = 1;
  if (gameStaus.value == 1) {
    Mineclearance(row, col);
  }
}
function setButtonRef(el: any, row: number, col: number) {
  if (!buttonRefs.value[row]) {
    buttonRefs.value[row] = [];
  }
  buttonRefs.value[row][col] = el;
}
//設定左鍵爬雷
function Mineclearance(row: number, col: number) {
  const currentButton = buttonRefs.value[row][col];
  if (currentButton.classList.contains("isOpen")) {
    return;
  } else if (currentButton.classList.contains("isNextMines")) {
    currentButton.classList.remove("isSave");
    currentButton.classList.add("isOpen");

    for (let j = row - 1; j <= row + 1; j++) {
      for (let k = col - 1; k <= col + 1; k++) {
        if (
          j >= 1 &&
          j <= initrow.value &&
          k >= 1 &&
          k <= initcol.value &&
          !(row === j && col === k)
        ) {
          let this_isMine = buttonRefs.value[j][k].classList.contains("isMine");
          if (this_isMine) {
            let setNumBtn: any = buttonRefs.value[row][col];
            let minesValue: number = isNaN(parseInt(setNumBtn.textContent))
              ? 1
              : parseInt(setNumBtn.textContent) + 1;
            setNumBtn.textContent = minesValue;
          }
        }
      }
    }
    return;
  } else if (currentButton.classList.contains("isSave")) {
    currentButton.classList.remove("isSave");
    currentButton.classList.add("isOpen");
  } else if (currentButton.classList.contains("isMine")) {
    //爆炸顯示全部雷
    gameStaus.value = 3;

    confetti.addConfetti();

    return;
  }

  for (let i = row - 1; i <= row + 1; i++) {
    for (let j = col - 1; j <= col + 1; j++) {
      if (
        i >= 1 &&
        i <= initrow.value &&
        j >= 1 &&
        j <= initcol.value &&
        !(row === i && col === j)
      ) {
        //九宮格內
        //有數字
        //沒有則繼續爬安全區
        console.log(i, j);
        let nextbutton = buttonRefs.value[i][j];
        if (
          nextbutton.textContent != null &&
          nextbutton.textContent.length > 0
        ) {
          currentButton.classList.remove("isSave");
          nextbutton.classList.add("isOpen");
        } else {
          Mineclearance(i, j);
        }
      }
    }
  }
}
//設定右鍵 下旗
//設定地雷
function setMines(clickrow: number, clickcol: number) {
  let max = miness.value;
  for (let i = 0; i < max; i++) {
    let minesrow = Math.ceil(Math.random() * initrow.value);
    let minescol = Math.ceil(Math.random() * initcol.value);
    const tempbutton = buttonRefs.value[minesrow][minescol];

    let checkmine = tempbutton.classList.contains("isMine");

    if (checkmine || (clickrow == minesrow && clickcol == minescol)) {
      i--;
      continue;
    } else {
      tempbutton.classList.add("isMine");
      tempbutton.classList.remove("isSave");
      if (tempbutton.classList.contains("isNextMines")) {
        tempbutton.classList.remove("isNextMines");
      }
      tempbutton.textContent = "";
    }
    for (let j = minesrow - 1; j <= minesrow + 1; j++) {
      for (let k = minescol - 1; k <= minescol + 1; k++) {
        if (
          j >= 1 &&
          j <= initrow.value &&
          k >= 1 &&
          k <= initcol.value &&
          !(minesrow === j && minescol === k)
        ) {
          let this_isMine = buttonRefs.value[j][k].classList.contains("isMine");
          console.log(this_isMine, j, k);
          if (this_isMine) {
            buttonRefs.value[j][k].textContent = "";
            continue;
          }
          let setNumBtn = buttonRefs.value[j][k];
          if (!setNumBtn.classList.contains("isNextMines")) {
            setNumBtn.classList.add("isNextMines");
          }
        }
      }
    }
  }
}
//搜尋地雷
//賦值按鈕
</script>
<!-- 點擊後#155c9e 點擊前  white-->
<style scoped>
.bordbase {
  display: flex;
  flex-direction: column;
  border-radius: 5px;
  padding: 10px;
  /* display: inline-block; */
  background-color: #1b71c8;
}
.bordbase button {
  position: relative; /* 相对定位 */
  white-space: nowrap;
  text-align: center;
  width: 50px;
  height: 50px;
  background-color: #e3ecf7;
  border-radius: 5px;
  margin: 1px;
  box-shadow: inset -1px -5px 0px 0px #a4cdfe;
}
.bordbase .isMine {
  background-color: red;
}
.bordbase .isOpen {
  align-self: stretch; /* 讓按鈕充滿父容器的高度 */
  background-color: transparent; /* 设置背景色为透明 */
  border: 2px solid black; /* 添加边框样式 */
  color: black; /* 设置文字颜色 */
  padding: 5px 10px; /* 设置内边距 */
  cursor: not-allowed;
  box-shadow: inset 0 0 5px 2px rgba(0, 0, 0, 0.5);
}
.isNextMines {
  align-self: stretch; /* 讓按鈕充滿父容器的高度 */
  color: transparent;
}
</style>
