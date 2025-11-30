<template>
  <div class="min-h-screen flex items-start justify-center bg-gradient-to-br from-green-100 via-blue-50 to-purple-100 py-8 relative">

    <!-- ОКНО НУМЕРАЦИИ ВОПРОСОВ -->
    <div
      class="absolute top-6 right-6 bg-white shadow-xl rounded-xl p-4 border border-green-300 w-72"
    >
      <h3 class="text-center font-bold mb-3 text-green-700">Вопросы</h3>

      <div class="grid grid-cols-5 gap-2">
        <button
          v-for="(q, idx) in totalQuestions"
          :key="idx"
          @click="jumpTo(idx)"
          class="rounded-md font-semibold flex items-center justify-center w-10 h-10 border
            transition"
          :class="{
            'bg-green-500 text-white': answered[idx],
            'bg-blue-500 text-white': idx === globalIndex,
            'bg-white text-gray-700 border-gray-300': !answered[idx] && idx !== globalIndex
          }"
        >
          {{ idx + 1 }}
        </button>
      </div>
    </div>

    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 border-t-4 border-green-400 mt-24">

      <h2 class="text-3xl font-bold text-center mb-2 text-green-700">
        🌍 Ударения в заимствованных словах
      </h2>

      <p class="text-center text-gray-500 mb-6">
        Определите, где падает ударение.
      </p>

      <div v-if="!finished">
        <div class="mb-4 text-lg font-semibold text-center text-green-700">
          Раздел {{ currentBlockIndex + 1 }} из {{ blocks.length }}:
          <b>{{ currentBlock.title }}</b>
        </div>

        <div v-if="!blockFinished">
          <div class="text-center text-xl mb-4" v-html="formattedSentence"></div>

          <input
            v-model="userAnswer"
            :disabled="showResult"
            placeholder="Введите слово с правильным ударением"
            @keyup.enter="checkAnswer"
            class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2
              focus:ring-green-400 mb-3 text-lg"
          />

          <!-- РЕЗУЛЬТАТ -->
          <div v-if="showResult" class="text-center">
            <p v-if="isCorrect" class="text-green-600 font-semibold mb-2">✅ Верно!</p>

            <p v-else class="text-red-600 font-semibold mb-2">
              ❌ Неверно. Правильно: <b>{{ currentQuestion.correct }}</b>
            </p>

            <!-- Аудио -->
            <audio v-if="currentQuestion.audio" :src="currentQuestion.audio" controls
              class="mx-auto my-2"></audio>

            <button
              @click="nextQuestion"
              class="mt-3 w-full bg-purple-600 hover:bg-purple-700 text-white font-bold
                py-2 px-4 rounded-lg transition"
            >
              Следующий
            </button>
          </div>

          <!-- КНОПКА "ПРОВЕРИТЬ" -->
          <button
            v-else
            @click="checkAnswer"
            class="w-full bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-lg transition"
          >
            Проверить
          </button>

          <!-- КНОПКИ НАЗАД / ВПЕРЁД -->
          <div class="flex justify-between mt-6">
            <button
              @click="goPrev"
              :disabled="globalIndex === 0"
              class="px-6 py-2 rounded-lg font-semibold border border-gray-300 bg-white
                hover:bg-gray-100 disabled:opacity-40"
            >
              ⬅ Назад
            </button>

            <button
              @click="goNext"
              :disabled="globalIndex === totalQuestions - 1"
              class="px-6 py-2 rounded-lg font-semibold border border-gray-300 bg-white
                hover:bg-gray-100 disabled:opacity-40"
            >
              Вперёд ➡
            </button>
          </div>

        </div>

        <!-- ПРАВИЛО -->
        <div v-else class="text-center mt-6 bg-green-50 p-6 rounded-xl border border-green-200">
          <p class="text-xl text-green-700 font-semibold mb-2">📘 Подсказка:</p>
          <p class="text-gray-700 italic">{{ currentBlock.rule }}</p>

          <button
            @click="nextBlock"
            class="mt-4 bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-6
              rounded-lg transition"
          >
            Далее
          </button>
        </div>
      </div>

      <!-- ФИНАЛ -->
      <div v-else class="text-center mt-6">
        <div class="bg-white rounded-xl shadow-lg p-6 inline-block">
          <p class="text-2xl font-bold text-green-700 mb-2">🎉 Все блоки пройдены!</p>

          <p class="text-3xl font-extrabold text-green-600 mb-2">
            {{ correctCount }}/{{ totalQuestions }} ({{ percent }}%)
          </p>

          <NuxtLink to="/" class="block text-blue-600 underline hover:text-blue-800 mt-2">
            ⬅ На главную
          </NuxtLink>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";

interface Question {
  sentence: string;
  correct: string;
  audio?: string;
}

interface Block {
  title: string;
  questions: Question[];
  rule: string;
}

const blocks: Block[] = [
  /* --- ВАШИ БЛОКИ БЕЗ ИЗМЕНЕНИЙ --- */
  {
    title: "🇫🇷 Французские заимствования",
    rule: "Слова из французского языка обычно имеют ударение на последнем слоге.",
    questions: [
      { sentence: "Портмоне лежало на столе.", correct: "портмонЕ" },
      { sentence: "Платье из ткани гофре.", correct: "гофрЕ" },
      { sentence: "Выступал квартет.", correct: "квартЕт" },
      { sentence: "Поезда стояли в депо.", correct: "депО" },
      { sentence: "Она опустила жалюзи.", correct: "жалюзИ" },
    ],
  },

  {
    title: "🇬🇧 Английские заимствования",
    rule: "В английских словах ударение часто на первом слоге.",
    questions: [
      { sentence: "Менеджмент — это наука.", correct: "мЕнеджмент" },
      { sentence: "Команда прошла тренинг.", correct: "трЕнинг" },
      { sentence: "Учитель включил таймер.", correct: "тАймер" },
      { sentence: "Маркетинг важен.", correct: "мАркетинг" },
      { sentence: "Ошибка на сервере.", correct: "сЕрвере" },
    ],
  },

  {
    title: "🇬🇷 Латинские и греческие",
    rule: "Ударение в латинских словах часто на одном из последних трёх слогов.",
    questions: [
      { sentence: "Алюминиевые рамы.", correct: "алюмИниевые" },
      { sentence: "Вставьте апостроф.", correct: "апострОф" },
      { sentence: "Учёный выдвинул гипотезу.", correct: "гипОтезу" },
      { sentence: "Школьный бюллетень.", correct: "бюллетЕнь" },
      { sentence: "Он пошёл в стоматологию.", correct: "стоматолОгия" },
    ],
  },
];

/* --- СОСТОЯНИЕ --- */
const currentBlockIndex = ref(0);
const currentQuestionIndex = ref(0);

const answered = ref<boolean[]>([]);
const globalIndex = computed(() => {
  let sum = 0;
  for (let i = 0; i < currentBlockIndex.value; i++) {
    sum += blocks[i].questions.length;
  }
  return sum + currentQuestionIndex.value;
});

/* --- ПОДСЧЁТ ВСЕГО --- */
const totalQuestions = blocks.reduce((a, b) => a + b.questions.length, 0);

/* ИНИЦИАЛИЗАЦИЯ МАССИВА ОТВЕЧЕННЫХ */
answered.value = Array(totalQuestions).fill(false);

/* КОМПЬЮТЕДЫ */
const currentBlock = computed(() => blocks[currentBlockIndex.value]);
const currentQuestion = computed(
  () => currentBlock.value.questions[currentQuestionIndex.value]
);

const userAnswer = ref("");
const showResult = ref(false);
const isCorrect = ref(false);
const finished = ref(false);
const blockFinished = ref(false);
const correctCount = ref(0);

const formattedSentence = computed(() => {
  const s = currentQuestion.value.sentence;
  const cw = currentQuestion.value.correct;
  const clean = cw.replace(/[Ёё]/g, (m) => m.toUpperCase());
  const reg = new RegExp(clean.replace(/[^А-Яа-яЁё]/g, ""), "i");
  return s.replace(reg, (m) => `<b>${m}</b>`);
});

/* --- ФУНКЦИИ --- */
function checkAnswer() {
  if (!userAnswer.value) return;

  showResult.value = true;
  isCorrect.value =
    userAnswer.value.trim().toLowerCase() ===
    currentQuestion.value.correct.toLowerCase();

  if (isCorrect.value) {
    correctCount.value++;
  }

  answered.value[globalIndex.value] = true;
}

function nextQuestion() {
  if (currentQuestionIndex.value + 1 < currentBlock.value.questions.length) {
    currentQuestionIndex.value++;
  } else {
    blockFinished.value = true;
  }

  userAnswer.value = "";
  showResult.value = false;
  isCorrect.value = false;
}

function nextBlock() {
  if (currentBlockIndex.value + 1 < blocks.length) {
    currentBlockIndex.value++;
    currentQuestionIndex.value = 0;
    blockFinished.value = false;
  } else {
    finished.value = true;
  }
}

function goPrev() {
  if (globalIndex.value === 0) return;
  jumpTo(globalIndex.value - 1);
}

function goNext() {
  if (globalIndex.value === totalQuestions - 1) return;
  jumpTo(globalIndex.value + 1);
}

function jumpTo(target: number) {
  let acc = 0;

  for (let i = 0; i < blocks.length; i++) {
    const size = blocks[i].questions.length;

    if (target < acc + size) {
      currentBlockIndex.value = i;
      currentQuestionIndex.value = target - acc;
      break;
    }
    acc += size;
  }

  userAnswer.value = "";
  showResult.value = false;
  isCorrect.value = false;
}

const percent = computed(() =>
  Math.round((correctCount.value / totalQuestions) * 100)
);
</script>

<style scoped>
button {
  transition: all 0.2s ease-in-out;
}
button:hover:not(:disabled) {
  transform: scale(1.05);
}
b {
  color: #10b981;
}
</style>
