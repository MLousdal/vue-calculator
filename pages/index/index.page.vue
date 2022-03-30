<script setup>
import { computed, reactive, onMounted, ref } from "vue";
import { useHead } from "@vueuse/head";

const siteData = reactive({
  title: `Vue Calculator`,
  description: `Frontend Mentor Challenge`,
});

useHead({
  // Can be static or computed
  title: computed(() => siteData.title),
  meta: [
    {
      name: `description`,
      content: computed(() => siteData.description),
    },
  ],
});

const themeSelector = ref(null);

let state = reactive({
  theme: 1,
  currentNumber: "",
  sum: 0,
  operator: null,
});

onMounted(() => {
  const userPrefersDark =
    window.matchMedia &&
    window.matchMedia("(prefers-color-scheme: dark)").matches;

  const userPrefersLight =
    window.matchMedia &&
    window.matchMedia("(prefers-color-scheme: light)").matches;

  if (userPrefersDark) state.theme = 3;
  if (userPrefersLight) state.theme = 1;

  changeTheme(state.theme);
});

function changeTheme(value = themeSelector.value.value) {
  let body = document.body;

  if (value == 1) {
    body.classList.remove("dark", "light");
    body.classList.add("blue");
    state.theme = 1;
  }
  if (value == 2) {
    body.classList.remove("dark", "blue");
    body.classList.add("light");
    state.theme = 2;
  }
  if (value == 3) {
    body.classList.remove("blue", "light");
    body.classList.add("dark");
    state.theme = 3;
  }
}

function addNum(e) {
  if (state.currentNumber == state.sum) state.currentNumber = "";
  state.currentNumber += e.target.value;
}

function addOperator(e) {
  state.operator = e.target.value;

  state.sum = calculate();

  state.currentNumber = "";
  console.log(state.sum);
}

function reset() {
  state.currentNumber = "";
  state.sum = 0;
  state.operator = null;
}

function del() {
  state.currentNumber = state.currentNumber.slice(0, -1);
}

function equal() {
  state.sum = calculate();
  state.currentNumber = state.sum;
}

function calculate(operator = state.operator) {
  if (operator == "+") return state.sum + parseFloat(state.currentNumber);
  if (state.sum !== 0) {
    if (operator == "-") return state.sum - parseFloat(state.currentNumber);
  } else {
    return parseFloat(state.currentNumber);
  }
  if (operator == "*") return state.sum * parseFloat(state.currentNumber);
  if (operator == "/") return state.sum / parseFloat(state.currentNumber);
}
</script>

<template>
  <main class="wrapper calculator">
    <header>
      <p class="text-5">calc</p>
      <div class="theme-slider">
        <p class="text-1">THEME</p>
        <div class="theme-numbers-container">
          <div class="theme-numbers">
            <p>1</p>
            <p>2</p>
            <p>3</p>
          </div>
          <input
            ref="themeSelector"
            type="range"
            min="1"
            max="3"
            :value="state.theme"
            @change="changeTheme(value)"
          />
        </div>
      </div>
    </header>
    <output>
      {{ state.currentNumber.length == 0 ? "0" : state.currentNumber }}
    </output>
    <section class="keyboard">
      <button value="7" @click="addNum">7</button>
      <button value="8" @click="addNum">8</button>
      <button value="9" @click="addNum">9</button>
      <button class="del" @click="del">DEL</button>
      <button value="4" @click="addNum">4</button>
      <button value="5" @click="addNum">5</button>
      <button value="6" @click="addNum">6</button>
      <button value="+" @click="addOperator">+</button>
      <button value="1" @click="addNum">1</button>
      <button value="2" @click="addNum">2</button>
      <button value="3" @click="addNum">3</button>
      <button value="-" @click="addOperator">-</button>
      <button value="." @click="addNum">.</button>
      <button value="0" @click="addNum">0</button>
      <button value="/" @click="addOperator">/</button>
      <button value="*" @click="addOperator">x</button>
      <button class="reset" @click="reset">RESET</button>
      <button class="equal" @click="equal">=</button>
    </section>
  </main>
</template>
