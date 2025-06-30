<script setup>
import { onMounted, ref } from 'vue'
import dayjs from 'dayjs'
import 'dayjs/locale/th'

// Store
const ActivitiesStore = useActivities()
const activities = ActivitiesStore.activities

const formatDateTime = (date) => {
  const timestamp = date < 10000000000 ? date * 1000 : date
  return dayjs(timestamp).locale('th').format('D MMMM YYYY HH:mm:ss')
}

// Modal state
const showModal = ref(false)
const editingActivity = ref({
  id: '',
  name: '',
  description: '',
})

// Open modal (exclude release_date)
const openModal = (activity) => {
  editingActivity.value = {
    id: activity.id,
    name: activity.name,
    description: activity.description
    // 👈 ไม่ใส่ release_date
  }
  showModal.value = true
}

const deleteActivity = async (id) => {
  const confirmDelete = confirm("Are you sure you want to delete this activity?");
  if (!confirmDelete) return;

  const success = await ActivitiesStore.deleteActivities(id);
  if (!success) {
    alert("Delete failed");
  }
};

const submitUpdate = async () => {
  const success = await ActivitiesStore.updateActivities(
    {
      name: editingActivity.value.name,
      description: editingActivity.value.description,
    },
    editingActivity.value.id
  );

  if (success) {
    await ActivitiesStore.fetchActivities()
    showModal.value = false;
  } else {
    alert("Update failed");
  }
};

onMounted(async () => {
  await ActivitiesStore.fetchActivities()
})
</script>

<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <div class="max-w-6xl mx-auto space-y-6">
      <h1 class="text-2xl font-bold text-gray-800">Activity List</h1>

      <div v-for="activity in activities" :key="activity.id"
        class="relative flex items-start gap-6 bg-white p-6 rounded-2xl shadow border border-gray-200">
        <!-- ปุ่ม Update และ Delete -->
        <div class="absolute top-4 right-4 flex gap-2">
          <button @click="openModal(activity)"
            class="text-sm bg-blue-500 hover:bg-blue-600 text-white px-4 py-1 rounded-full shadow transition">
            ✏️ Update
          </button>
          <button @click="deleteActivity(activity.id)"
            class="text-sm bg-red-500 hover:bg-red-600 text-white px-4 py-1 rounded-full shadow transition">
            🗑️ Delete
          </button>
        </div>

        <div
          class="flex-shrink-0 w-16 h-16 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 font-bold text-xl">
          📝
        </div>

        <div class="flex flex-col gap-1">
          <h2 class="text-lg font-semibold text-gray-800">{{ activity.name }}</h2>
          <p class="text-sm text-gray-500">Created at: {{ formatDateTime(activity.created_at) }}</p>
          <p class="text-gray-700">{{ activity.description }}</p>
        </div>
      </div>

      <div class="flex justify-end">
        <NuxtLink to="/create">
          <button class="bg-rose-500 hover:bg-rose-600 text-white font-medium px-6 py-2 rounded-full shadow transition">
            ➕ Create New Activity
          </button>
        </NuxtLink>
      </div>
    </div>

    <!-- Modal -->
    <div v-if="showModal" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center">
      <div class="bg-white rounded-2xl p-8 w-full max-w-lg shadow-xl space-y-6 relative">
        <h2 class="text-xl font-bold text-gray-800">🛠️ Edit Activity</h2>

        <form @submit.prevent="submitUpdate" class="space-y-4">
          <div>
            <label class="block text-gray-700 font-medium mb-1">Activity Name</label>
            <input v-model="editingActivity.name" type="text"
              class="w-full border border-gray-300 rounded-xl px-4 py-2 focus:ring-2 focus:ring-blue-500 focus:outline-none"
              required />
          </div>

          <div>
            <label class="block text-gray-700 font-medium mb-1">Description</label>
            <textarea v-model="editingActivity.description" rows="3"
              class="w-full border border-gray-300 rounded-xl px-4 py-2 focus:ring-2 focus:ring-blue-500 focus:outline-none"
              required></textarea>
          </div>

          <div class="flex justify-end gap-3">
            <button type="button" @click="showModal = false"
              class="px-4 py-2 border border-gray-300 rounded-xl text-gray-600 hover:bg-gray-100">
              Cancel
            </button>
            <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-xl">
              Save
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
