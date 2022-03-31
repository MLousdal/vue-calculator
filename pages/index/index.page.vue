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
  display: 0,
});

onMounted(() => {
  const userPrefersDark =
    window.matchMedia &&
    window.matchMedia("(prefers-color-scheme: dark)").matches;

  const userPrefersLight =
    window.matchMedia &&
    window.matchMedia("(prefers-color-scheme: light)").matches;

  if (userPrefersDark) state.theme = 1;
  if (userPrefersLight) state.theme = 2;

  changeTheme(state.theme);

  document.addEventListener("keydown", (e) => {
    if (!isNaN(Number(e.key))) {
      addNum(e.key);
    }

    switch (e.key) {
      case "+":
        addOperator("+");
        break;
      case "-":
        addOperator("-");
        break;
      case "*":
        addOperator("*");
        break;
      case "/":
        addOperator("/");
        break;
      case "Enter":
        equal();
        break;

      default:
        break;
    }
  });

  updateDisplay();
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

function addNum(num) {
  if (state.display === state.sum) return;
  if (num instanceof PointerEvent) num = num.target.value;
  state.currentNumber += num;
  updateDisplay();
}

function addOperator(operator) {
  if (state.display !== state.sum) state.sum = calculate();
  if (operator instanceof PointerEvent) operator = operator.target.value;

  state.operator = operator;
  state.currentNumber = "";
  updateDisplay();
}

function reset() {
  state.currentNumber = "";
  state.sum = 0;
  state.operator = null;
  updateDisplay();
}

function del() {
  if (state.display === state.sum) return;

  state.currentNumber = state.currentNumber.slice(0, -1);
  updateDisplay();
}

function equal() {
  state.sum = calculate();
  if (isNaN(state.sum)) state.sum = 0;

  state.operator = null;
  state.currentNumber = "";
  state.display = state.sum;
}

function calculate(operator = state.operator) {
  if (operator == "+") return state.sum + parseFloat(state.currentNumber);
  if (operator == "*") return state.sum * parseFloat(state.currentNumber);
  if (operator == "/") return state.sum / parseFloat(state.currentNumber);
  if (operator == "-") return state.sum - parseFloat(state.currentNumber);

  return parseFloat(state.currentNumber);
}

function updateDisplay() {
  state.display = `${state.sum == 0 ? "" : state.sum}${
    state.operator ? state.operator : ""
  }${state.currentNumber.length == 0 ? 0 : state.currentNumber}`;
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
      {{ state.display }}
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
