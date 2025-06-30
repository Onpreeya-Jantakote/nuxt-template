<template>
  <div class="min-h-screen flex items-center justify-center p-6">
    <div class="bg-white rounded-2xl shadow-xl w-full max-w-xl p-8">
      <h2 class="text-2xl font-bold text-gray-800 mb-6">Create New Activity</h2>

      <form @submit.prevent="handleSubmit" class="space-y-6">
        <!-- Activity Name -->
        <div>
          <label for="name" class="block text-gray-700 font-medium mb-1">Activity Name</label>
          <input
            v-model="name"
            type="text"
            id="name"
            name="name"
            required
            placeholder="Enter activity name"
            class="w-full border border-gray-300 rounded-xl px-4 py-3 text-gray-800 focus:outline-none focus:ring-2 focus:ring-blue-500 transition shadow-sm"
          />
        </div>

        <!-- Description -->
        <div>
          <label for="description" class="block text-gray-700 font-medium mb-1">Description</label>
          <textarea
            v-model="description"
            id="description"
            name="description"
            required
            rows="4"
            placeholder="Enter description"
            class="w-full border border-gray-300 rounded-xl px-4 py-3 text-gray-800 resize-none focus:outline-none focus:ring-2 focus:ring-blue-500 transition shadow-sm"
          ></textarea>
        </div>

        <!-- Release Date -->
        <div>
          <label for="release_date" class="block text-gray-700 font-medium mb-1">Release Date</label>
          <input
            v-model="release_date"
            type="date"
            id="release_date"
            name="release_date"
            required
            class="w-full border border-gray-300 rounded-xl px-4 py-3 text-gray-800 focus:outline-none focus:ring-2 focus:ring-blue-500 transition shadow-sm"
          />
        </div>

        <!-- Submit Button -->
        <div class="flex justify-end">
          <button
            type="submit"
            class="bg-blue-600 hover:bg-blue-700 text-white font-semibold px-6 py-3 rounded-xl shadow-md transition cursor-pointer"
          >
            Create Activity
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useActivities } from '../composables/activities'

const router = useRouter()
const ActivitiesStore = useActivities()

const name = ref('')
const description = ref('')

const handleSubmit = async () => {
    const payload = {
        name: name.value,
        description: description.value,
    }

    console.log('Form submitted with payload:', payload)

    const success = await ActivitiesStore.createActivities(payload)
    if (success) {
        router.push('/')
    }
}
</script>

<style lang="scss" scoped></style>
