
<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-blue-100 to-purple-100 py-8">
    <div class="w-full max-w-md bg-white rounded-2xl shadow-xl p-8">
      <h2 class="text-2xl font-bold text-center mb-2 text-purple-700">Квест: Исправь ошибку</h2>
      <p class="text-center text-gray-500 mb-6">В каждом слове или предложении есть ошибка. Исправьте её.</p>
      <div v-if="!finished">
        <div class="mb-4 text-lg text-center">
          <span v-html="currentTask.question" class="font-semibold text-blue-700"></span>
        </div>
        <input
          v-model="userAnswer"
          :disabled="showResult"
          placeholder="Ваш вариант"
          @keyup.enter="checkAnswer"
          class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-400 mb-3 text-lg"
        />
        <div v-if="showResult">
          <p v-if="isCorrect" class="text-green-600 font-semibold text-center mb-2">Верно!</p>
          <p v-else class="text-red-600 font-semibold text-center mb-2">Неверно. Правильный ответ: <b>{{ currentTask.answer }}</b></p>
          <button @click="nextTask" class="w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg transition mb-2">Следующий</button>
        </div>
        <button v-else @click="checkAnswer" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg transition">Проверить</button>
      </div>
      <div v-else class="text-center mt-6">
        <div class="bg-white rounded-xl shadow-lg p-6 inline-block">
          <p class="text-2xl font-bold text-purple-700 mb-2">Квест завершён!</p>
          <p class="text-lg mb-2">Ваш результат:</p>
          <p class="text-3xl font-extrabold text-green-600 mb-2">{{ correctCount }}/{{ tasks.length }} ({{ percent }}%)</p>
          <NuxtLink to="/" class="text-blue-600 underline hover:text-blue-800">На главную</NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const tasks = [
  { question: 'Исправьте ошибку: <b>маленкий</b>', answer: 'маленький' },
  { question: 'Исправьте ошибку: <b>инжинер</b>', answer: 'инженер' },
  { question: 'Исправьте ошибку: <b>весёлыйе</b>', answer: 'весёлые' },
  { question: 'Исправьте ошибку: <b>пироженое</b>', answer: 'пирожное' },
  { question: 'Исправьте ошибку: <b>девчёнка</b>', answer: 'девчонка' },
  { question: 'Исправьте ошибку: <b>зделать</b>', answer: 'сделать' },
  { question: 'Исправьте ошибку: <b>чюдо</b>', answer: 'чудо' },
  { question: 'Исправьте ошибку: <b>пажалуйста</b>', answer: 'пожалуйста' },
  { question: 'Исправьте ошибку: <b>пакраснел</b>', answer: 'покраснел' },
  { question: 'Исправьте ошибку: <b>сдесь</b>', answer: 'здесь' },
];

const currentIdx = ref(0);
const userAnswer = ref('');
const showResult = ref(false);
const isCorrect = ref(false);
const correctCount = ref(0);

const currentTask = computed(() => tasks[currentIdx.value] || null);
const finished = computed(() => currentIdx.value >= tasks.length);
const percent = computed(() => Math.round((correctCount.value / tasks.length) * 100));

function checkAnswer() {
  if (!currentTask.value) return;
  showResult.value = true;
  isCorrect.value = userAnswer.value.trim().toLowerCase() === currentTask.value.answer.toLowerCase();
  if (isCorrect.value) correctCount.value++;
}

function nextTask() {
  currentIdx.value++;
  userAnswer.value = '';
  showResult.value = false;
  isCorrect.value = false;
}
</script>


<!-- TailwindCSS стили применяются через классы -->
