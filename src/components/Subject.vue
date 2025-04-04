<template>
  <section class="relative w-full bg-gray-900 text-white py-16" id="subject">
    <div class="container mx-auto px-6">
      <!-- Subject List -->
      <div class="mt-10 bg-gray-800 shadow-lg rounded-lg p-6">
        <h2 class="text-xl font-semibold mb-4">Subjects</h2>
        <table class="w-full border-collapse border border-gray-700 text-white">
          <thead>
            <tr class="bg-gray-700">
              <th class="border border-gray-600 p-2">ID</th>
              <th class="border border-gray-600 p-2">Name</th>
              <th class="border border-gray-600 p-2">Credits</th>
              <th class="border border-gray-600 p-2">Grade</th>
              <th class="border border-gray-600 p-2">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="subject in subjects" :key="subject.id" class="text-center">
              <td class="border border-gray-600 p-2">
                <input v-if="editingSubject && originalId === subject.id" v-model="editingSubject.id" class="w-full p-2 border border-gray-600 bg-gray-700 text-white" />
                <span v-else>{{ subject.id }}</span>
              </td>
              <td class="border border-gray-600 p-2">
                <input v-if="editingSubject && originalId === subject.id" v-model="editingSubject.name" class="w-full p-2 border border-gray-600 bg-gray-700 text-white" />
                <span v-else>{{ subject.name }}</span>
              </td>
              <td class="border border-gray-600 p-2">
                <select v-if="editingSubject && originalId === subject.id" v-model="editingSubject.credit" class="w-full p-2 border border-gray-600 bg-gray-700 text-white">
                  <option v-for="credit in creditOptions" :key="credit" :value="credit">{{ credit }}</option>
                </select>
                <span v-else>{{ subject.credit }}</span>
              </td>
              <td class="border border-gray-600 p-2">
                <select v-if="editingSubject && originalId === subject.id" v-model="editingSubject.grade" class="w-full p-2 border border-gray-600 bg-gray-700 text-white">
                  <option v-for="grade in gradeOptions" :key="grade" :value="grade">{{ grade }}</option>
                </select>
                <span v-else>{{ subject.grade }}</span>
              </td>
              <td class="border border-gray-600 p-2">
                <button v-if="editingSubject && originalId === subject.id" @click="editSubject" class="bg-blue-500 text-white px-4 py-2 rounded">Save</button>
                <button v-if="editingSubject && originalId === subject.id" @click="() => { editingSubject = null; originalId = '' }" class="bg-gray-500 text-white px-4 py-2 rounded">Cancel</button>
                <button v-else @click="() => { editingSubject = { ...subject }; originalId = subject.id }" class="bg-yellow-500 text-white px-4 py-2 rounded">Edit</button>
                <button @click="deleteSubject(subject.id)" class="bg-red-500 text-white px-4 py-2 rounded ml-2">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Add Subject -->
      <div class="mt-10 bg-gray-800 shadow-lg rounded-lg p-6">
        <h2 class="text-xl font-semibold mb-4">Add New Subject</h2>
        <form @submit.prevent="addSubject">
          <input v-model="newSubject.id" class="block w-full p-2 border border-gray-600 rounded mt-2 bg-gray-700 text-white" placeholder="Subject ID" required />
          <input v-model="newSubject.name" class="block w-full p-2 border border-gray-600 rounded mt-2 bg-gray-700 text-white" placeholder="Subject Name" required />
          <label class="block mt-2">Credit</label>
          <select v-model="newSubject.credit" class="block w-full p-2 border border-gray-600 rounded mt-2 bg-gray-700 text-white" required>
            <option v-for="credit in creditOptions" :key="credit" :value="credit">{{ credit }}</option>
          </select>
          <label class="block mt-2">Grade</label>
          <select v-model="newSubject.grade" class="block w-full p-2 border border-gray-600 rounded mt-2 bg-gray-700 text-white" required>
            <option v-for="grade in gradeOptions" :key="grade" :value="grade">{{ grade }}</option>
          </select>
          <div class="flex justify-end mt-4">
            <button type="submit" class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 transition">Add Subject</button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

interface Subject {
  id: string;
  name: string;
  credit: string;
  grade: string;
}

const subjects = ref<Subject[]>([]);
const editingSubject = ref<Subject | null>(null);
const originalId = ref<string>('');
const newSubject = ref<Subject>({ id: '', name: '', credit: '1', grade: 'A' });

const creditOptions = ['1', '2', '3', '4'];
const gradeOptions = ['A', 'B+', 'B', 'C+', 'C', 'D+', 'D', 'F'];

const fetchSubjects = async () => {
  try {
    const response = await fetch('http://localhost:3000/subjects');
    if (!response.ok) throw new Error('Failed to fetch subjects');
    subjects.value = await response.json();
  } catch (error) {
    console.error(error);
  }
};

const addSubject = async () => {
  try {
    const response = await fetch('http://localhost:3000/subjects', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(newSubject.value),
    });
    if (!response.ok) throw new Error('Failed to add subject');
    fetchSubjects();
    newSubject.value = { id: '', name: '', credit: '1', grade: 'A' };
  } catch (error) {
    console.error(error);
  }
};

const editSubject = async () => {
  if (!editingSubject.value) return;
  try {
    if (originalId.value !== editingSubject.value.id) {
      // ถ้า ID ถูกเปลี่ยน → ลบตัวเดิม + เพิ่มใหม่
      await fetch(`http://localhost:3000/subjects/${originalId.value}`, {
        method: 'DELETE',
      });

      await fetch(`http://localhost:3000/subjects`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(editingSubject.value),
      });
    } else {
      // ถ้า ID เดิม → แค่ update
      await fetch(`http://localhost:3000/subjects/${originalId.value}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(editingSubject.value),
      });
    }

    fetchSubjects();
    editingSubject.value = null;
    originalId.value = '';
  } catch (error) {
    console.error(error);
  }
};

const deleteSubject = async (id: string) => {
  try {
    const response = await fetch(`http://localhost:3000/subjects/${id}`, {
      method: 'DELETE',
    });
    if (!response.ok) throw new Error('Failed to delete subject');
    fetchSubjects();
  } catch (error) {
    console.error(error);
  }
};

onMounted(() => {
  fetchSubjects();
});
</script>
