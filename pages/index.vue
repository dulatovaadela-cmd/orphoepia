<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-softpink py-8">
    <div class="text-center mb-10 animate-fade-in">
      <h1 class="text-4xl font-extrabold text-pink-600 mb-2">Орфоэпия — легко и интересно!</h1>
      <p class="text-lg text-gray-700">Выбери игру и проверь свои знания 😊</p>
    </div>

    <div class="w-full max-w-md bg-white rounded-2xl shadow-2xl p-8 flex flex-col items-center space-y-6">
      <NuxtLink
        to="/stress"
        class="w-full py-4 px-6 bg-pink-500 hover:bg-pink-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition transform hover:scale-105"
      >
        🎯 Тест: Ударение
      </NuxtLink>

      <NuxtLink
        to="/quiz"
        class="w-full py-4 px-6 bg-purple-600 hover:bg-purple-700 text-white text-lg font-semibold rounded-xl text-center shadow-md transition transform hover:scale-105"
      >
        🧠 Викторина
      </NuxtLink>

      <NuxtLink
        to="/sentence-accent"
        class="w-full py-4 px-6 bg-blue-500 hover:bg-blue-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition transform hover:scale-105"
      >
        🗣️ Укажи верное ударение
      </NuxtLink>

      <NuxtLink
        to="/find-mistake"
        class="w-full py-4 px-6 bg-yellow-500 hover:bg-yellow-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition transform hover:scale-105"
      >
        ✍️ Найди ошибку
      </NuxtLink>

      <NuxtLink
        to="/borrowings"
        class="w-full py-4 px-6 bg-green-500 hover:bg-green-600 text-white text-lg font-semibold rounded-xl text-center shadow-md transition transform hover:scale-105"
      >
        🌍 Заимствованные слова
      </NuxtLink>
    </div>

    <footer class="mt-10 text-sm text-gray-500">
      © 2025 Учебный проект | Создано с ❤️ для школы
    </footer>
  </div>
</template>

<script setup lang="ts">
// Просто список заданий
</script>

<style scoped>
/* Пастельно-розовый фон */
.bg-softpink {
  background-color: #ffe6f2;
}

@keyframes fade-in {
  from { opacity: 0; transform: translateY(15px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fade-in {
  animation: fade-in 0.8s ease-in-out;
}
</style>
