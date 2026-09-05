<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-softpink py-8 px-4">

    <div class="text-center mb-8 animate-fade-in">
      <h1 class="text-4xl font-extrabold text-pink-600 mb-2">
        Орфоэпия — легко и интересно!
      </h1>

      <p class="text-lg text-gray-700">
        Сначала представься, а затем выбери задание 😊
      </p>
    </div>

    <div class="w-full max-w-md bg-white rounded-2xl shadow-2xl p-8">

      <h2 class="text-2xl font-bold text-center text-purple-700 mb-6">
        Участник
      </h2>

      <div class="mb-4">
        <label class="block text-gray-700 font-semibold mb-2">
          Имя и фамилия
        </label>

        <input
          v-model="studentName"
          type="text"
          placeholder="Например: Иван Иванов"
          class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-purple-400"
        />
      </div>

      <div class="mb-6">
        <label class="block text-gray-700 font-semibold mb-2">
          Класс
        </label>

        <input
          v-model="studentClass"
          type="text"
          placeholder="Например: 7А"
          class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-purple-400"
        />
      </div>

      <p
        v-if="errorMessage"
        class="text-red-600 text-center font-semibold mb-4"
      >
        {{ errorMessage }}
      </p>

      <button
        @click="start"
        class="w-full py-4 px-6 bg-purple-600 hover:bg-purple-700 text-white text-lg font-semibold rounded-xl shadow-md transition transform hover:scale-[1.02] mb-6"
      >
        🚀 Начать
      </button>

      <div class="flex flex-col gap-4">

        <NuxtLink
          to="/stress"
          @click="goToTask('/stress')"
          class="w-full py-4 px-6 bg-pink-500 hover:bg-pink-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
        >
          🎯 Тест: Ударение
        </NuxtLink>

        <NuxtLink
          to="/quiz"
          @click="goToTask('/quiz')"
          class="w-full py-4 px-6 bg-purple-600 hover:bg-purple-700 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
        >
          🧠 Викторина
        </NuxtLink>

        <NuxtLink
          to="/sentence-accent"
          @click="goToTask('/sentence-accent')"
          class="w-full py-4 px-6 bg-blue-500 hover:bg-blue-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
        >
          🗣️ Укажи верное ударение
        </NuxtLink>

        <NuxtLink
          to="/find-mistake"
          @click="goToTask('/find-mistake')"
          class="w-full py-4 px-6 bg-yellow-500 hover:bg-yellow-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
        >
          ✍️ Найди ошибку
        </NuxtLink>

        <NuxtLink
          to="/borrowings"
          @click="goToTask('/borrowings')"
          class="w-full py-4 px-6 bg-green-500 hover:bg-green-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
        >
          🌍 Заимствованные слова
        </NuxtLink>

      </div>
    </div>

    <footer class="mt-10 text-sm text-gray-500">
      © 2025 Учебный проект | Создано с ❤️ для школы
    </footer>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const studentName = ref('')
const studentClass = ref('')
const errorMessage = ref('')

function start() {
  errorMessage.value = ''

  if (!studentName.value.trim()) {
    errorMessage.value = 'Введите имя и фамилию'
    return
  }

  if (!studentClass.value.trim()) {
    errorMessage.value = 'Введите класс'
    return
  }

  localStorage.setItem(
    'orphoepia_student',
    JSON.stringify({
      name: studentName.value.trim(),
      class: studentClass.value.trim()
    })
  )

  navigateTo('/stress')
}

function goToTask(path: string) {
  errorMessage.value = ''

  if (!studentName.value.trim() || !studentClass.value.trim()) {
    errorMessage.value = 'Сначала введите имя и класс'
    return
  }

  localStorage.setItem(
    'orphoepia_student',
    JSON.stringify({
      name: studentName.value.trim(),
      class: studentClass.value.trim()
    })
  )

  navigateTo(path)
}
</script>

<style scoped>
.bg-softpink {
  background-color: #ffe6f2;
}

@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(15px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fade-in 0.8s ease-in-out;
}
</style>
