<template>
  <div class="min-h-screen bg-softpink py-8 px-4">

    <div class="max-w-6xl mx-auto">

      <!-- Заголовок -->
      <div class="text-center mb-8 animate-fade-in">
        <h1 class="text-4xl font-extrabold text-pink-600 mb-2">
          Орфоэпия — легко и интересно!
        </h1>

        <p class="text-lg text-gray-700">
          Представься и выбери задание 😊
        </p>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-4 gap-6">

        <!-- ЛЕВАЯ ПАНЕЛЬ -->
        <aside class="lg:col-span-1">

          <div class="bg-white rounded-2xl shadow-2xl p-6 lg:sticky lg:top-6">

            <h2 class="text-2xl font-bold text-purple-700 text-center mb-5">
              👤 Участник
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

            <div class="mb-5">
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

            <!-- Мой прогресс -->
            <NuxtLink
              to="/results"
              @click="openProgress"
              class="block w-full py-4 px-4 bg-purple-600 hover:bg-purple-700 text-white text-lg font-semibold rounded-xl text-center shadow-md transition"
            >
              📊 Мой прогресс
            </NuxtLink>

            <p class="text-sm text-gray-500 text-center mt-3">
              Здесь будут ваши результаты
            </p>

          </div>

        </aside>

        <!-- ПРАВАЯ ЧАСТЬ — ЗАДАНИЯ -->
        <main class="lg:col-span-3">

          <div class="bg-white rounded-2xl shadow-2xl p-8">

            <h2 class="text-2xl font-bold text-purple-700 text-center mb-6">
              🎯 Выберите задание
            </h2>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-5">

              <NuxtLink
                to="/stress"
                @click="goToTask('/stress')"
                class="py-6 px-5 bg-pink-500 hover:bg-pink-600 text-white text-lg font-semibold rounded-2xl text-center shadow-md transition transform hover:scale-[1.02]"
              >
                🎯 Тест: Ударение
              </NuxtLink>

              <NuxtLink
                to="/quiz"
                @click="goToTask('/quiz')"
                class="py-6 px-5 bg-purple-600 hover:bg-purple-700 text-white text-lg font-semibold rounded-2xl text-center shadow-md transition transform hover:scale-[1.02]"
              >
                🧠 Викторина
              </NuxtLink>

              <NuxtLink
                to="/sentence-accent"
                @click="goToTask('/sentence-accent')"
                class="py-6 px-5 bg-blue-500 hover:bg-blue-600 text-white text-lg font-semibold rounded-2xl text-center shadow-md transition transform hover:scale-[1.02]"
              >
                🗣️ Укажи верное ударение
              </NuxtLink>

              <NuxtLink
                to="/find-mistake"
                @click="goToTask('/find-mistake')"
                class="py-6 px-5 bg-yellow-500 hover:bg-yellow-600 text-white text-lg font-semibold rounded-2xl text-center shadow-md transition transform hover:scale-[1.02]"
              >
                ✍️ Найди ошибку
              </NuxtLink>

              <NuxtLink
                to="/borrowings"
                @click="goToTask('/borrowings')"
                class="py-6 px-5 bg-green-500 hover:bg-green-600 text-white text-lg font-semibold rounded-2xl text-center shadow-md transition transform hover:scale-[1.02] md:col-span-2"
              >
                🌍 Заимствованные слова
              </NuxtLink>

            </div>

          </div>

        </main>

      </div>

    </div>

    <footer class="mt-10 text-sm text-gray-500 text-center">
      © 2025 Учебный проект | Создано с ❤️ для школы
    </footer>

  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const studentName = ref('')
const studentClass = ref('')
const errorMessage = ref('')

function saveStudent() {
  localStorage.setItem(
    'orphoepia_student',
    JSON.stringify({
      name: studentName.value.trim(),
      class: studentClass.value.trim()
    })
  )
}

function validateStudent() {
  errorMessage.value = ''

  if (!studentName.value.trim()) {
    errorMessage.value = 'Введите имя и фамилию'
    return false
  }

  if (!studentClass.value.trim()) {
    errorMessage.value = 'Введите класс'
    return false
  }

  return true
}

function goToTask(path: string) {
  if (!validateStudent()) {
    return
  }

  saveStudent()
  navigateTo(path)
}

function openProgress() {
  if (!validateStudent()) {
    return
  }

  saveStudent()
}

onMounted(() => {
  const savedStudent = localStorage.getItem('orphoepia_student')

  if (savedStudent) {
    try {
      const student = JSON.parse(savedStudent)

      studentName.value = student.name || ''
      studentClass.value = student.class || ''
    } catch {
      localStorage.removeItem('orphoepia_student')
    }
  }
})
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
