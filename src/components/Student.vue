<template> 
  <section class="relative w-full min-h-screen bg-gray-900 text-white" id="student">
    <div class="absolute top-0 inset-x-0 h-64 flex items-start">
      <div class="h-24 w-2/3 bg-gradient-to-br from-[#570cac] blur-2xl opacity-40"></div>
      <div class="h-20 w-3/5 bg-gradient-to-r from-[#670ccf] opacity-40 blur-2xl"></div>
    </div>

    <div class="w-full px-6 sm:px-10 md:px-16 lg:px-12 max-w-screen-lg lg:max-w-screen-xl mx-auto relative">
      <div class="grid lg:grid-cols-2 gap-12 xl:gap-16 pt-24 lg:max-w-none max-w-2xl md:max-w-3xl mx-auto items-center">

        <!-- Image Section (Left Side) -->
        <div class="lg:h-full md:flex justify-center lg:justify-start">
          <div class="relative w-80 h-80 lg:w-96 lg:h-96 rounded-full overflow-hidden border-4 border-primary shadow-lg">
            <img src="@/assets/nisit.jpg" alt="Student Picture" class="w-full h-full rounded-full object-cover">
          </div>
        </div>

        <!-- Student Information (Right Side) -->
        <div class="lg:w-full text-center lg:text-left px-6">
          <h1 class="text-5xl md:text-6xl font-bold text-white mb-8">ข้อมูลนิสิต</h1>

          <!-- Display Student Data -->
          <div v-if="student && !editMode" class="bg-gray-800 p-8 rounded-xl shadow-lg text-xl">
            <p class="mb-4"><strong class="text-blue-400">ชื่อ-นามสกุล:</strong> {{ student.fullname }}</p>
            <p class="mb-4"><strong class="text-blue-400">รหัสนิสิต:</strong> {{ student.id }}</p>
            <p class="mb-4"><strong class="text-blue-400">สาขา:</strong> {{ student.major }}</p>
            <p class="mb-4"><strong class="text-blue-400">โรงเรียนเดิม:</strong> {{ student.old_school }}</p>
            <button @click="editMode = true" class="mt-6 px-8 py-3 bg-blue-500 hover:bg-blue-600 text-white font-bold rounded-lg text-lg transition">แก้ไขข้อมูล</button>
          </div>

          <!-- Edit Mode -->
          <div v-if="editMode" class="mt-6 bg-gray-800 p-8 rounded-xl shadow-lg text-xl">
            <h2 class="text-3xl mb-6 font-semibold">แก้ไขข้อมูลนิสิต</h2>
            <form @submit.prevent="updateStudent" class="space-y-6">
              <div class="flex flex-col">
                <label class="font-semibold">ชื่อ-นามสกุล:</label>
                <input v-model="student.fullname" class="p-3 bg-gray-700 border border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-lg" />
              </div>
              <div class="flex flex-col">
                <label class="font-semibold">รหัสนิสิต:</label>
                <input v-model="student.id" class="p-3 bg-gray-700 border border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-lg" />
              </div>
              <div class="flex flex-col">
                <label class="font-semibold">สาขา:</label>
                <input v-model="student.major" class="p-3 bg-gray-700 border border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-lg" />
              </div>
              <div class="flex flex-col">
                <label class="font-semibold">โรงเรียนเดิม:</label>
                <input v-model="student.old_school" class="p-3 bg-gray-700 border border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-lg" />
              </div>
              <div class="flex gap-6 mt-6">
                <button type="submit" class="px-8 py-3 bg-green-500 hover:bg-green-600 text-white font-bold rounded-lg text-lg transition">บันทึก</button>
                <button type="button" @click="cancelEdit" class="px-8 py-3 bg-red-500 hover:bg-red-600 text-white font-bold rounded-lg text-lg transition">ยกเลิก</button>
              </div>
            </form>
          </div>
        </div>

      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const student = ref(null);
const editMode = ref(false);
const originalStudent = ref(null);

const fetchStudent = async () => {
  try {
    const res = await fetch('http://localhost:3000/student');
    student.value = await res.json();
    originalStudent.value = { ...student.value };
  } catch (error) {
    console.error("Error fetching student data:", error);
  }
};

const updateStudent = async () => {
  await fetch('http://localhost:3000/student', {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(student.value),
  });

  editMode.value = false;
  fetchStudent();
};

const cancelEdit = () => {
  student.value = { ...originalStudent.value };
  editMode.value = false;
};

onMounted(fetchStudent);
</script>
