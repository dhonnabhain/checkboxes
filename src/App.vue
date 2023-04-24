<script setup>
import { ref } from 'vue'
import dataset from './dataset.json'

const checked = ref([])

function onCheckParent(e) {
  // Grab selected item value
  const value = e.target.value
  const item = dataset.find(item => item.title === value)

  // Grab all children title if present
  const children = item.children ? item.children.map(child => child.title) : []

  // Merge data into one array to simplify process
  const valuesToProcess = [value, ...children]

  // If selected item already set, remove it and all it's children
  // Else, add new selected input and all it's children
  if (checked.value.includes(value)) {
    // Assignate a filtred version of the current selected state
    // Here, we test each checked value against the new selected input and it's children
    checked.value = checked.value.filter(val => !valuesToProcess.includes(val))
  } else {
    // Create a set based on current selected state AND the new input + children
    // This allow us to ensure there is no duplicate values
    const uniqueValues = new Set([...checked.value, ...valuesToProcess])

    // Assign Set result as a array to the checked state
    checked.value = Array.from(uniqueValues)
  }

  // Tada ! 🎉
}
</script>

<template>
  <main class="max-w-xl mx-auto py-6">
    <form class="space-y-8">
      <div v-for="item in dataset" class="space-y-3">
        <div class="flex gap-x-4 py-2 px-3 bg-slate-200">
          <input
            :id="item.title"
            :value="item.title"
            type="checkbox"
            @input="onCheckParent"
          />
          <label :for="item.title" class="text-xl font-medium cursor-pointer">
            {{ item.title }}
          </label>
        </div>

        <ul v-if="item.children && item.children.length > 0" class="pl-10">
          <li v-for="child in item.children" class="flex gap-x-4">
            <input
              v-model="checked"
              :id="child.title"
              :value="child.title"
              type="checkbox"
            />
            <label :for="child.title" class="font-medium cursor-pointer">
              {{ child.title }}
            </label>
          </li>
        </ul>
      </div>
    </form>

    <section class="mt-8 space-y-2">
      <h3 class="font-bold">Checked State:</h3>
      <pre>
        {{ checked }}
      </pre>
    </section>
  </main>
</template>
