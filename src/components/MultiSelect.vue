<template>
  <div>
    <label for="roles">Choose a role:</label>
    <select
      id="roles"
      v-model="multi"
      name="roles"
    >
      <option
        v-for="role in roles"
        :key="role"
        :value="role"
      >
        {{ role }}
      </option>
    </select>
    <button
      class="p-2 ml-2 border"
      @click="submitChoice"
    >
      submit select
    </button>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useContactsStore } from '@/store'
import { storeToRefs } from 'pinia'

const multi = ref<string>('admin')
const contactsStore = useContactsStore()
const { roles } = storeToRefs(contactsStore)

function submitChoice () {
  emit('choice', multi.value)
}

const emit = defineEmits(['choice'])
</script>
