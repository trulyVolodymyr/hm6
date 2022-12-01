<template>
  <div class="p-5">
    <AppInput v-model.trim="searchName" placeholder="Search for user" />
  </div>

  <div class="p-5">
    <MultiSelect @choice="defineChoice" />
  </div>

  <div class="p-5">
    <h3>Sort Card by:</h3>
    <div class="flex mt-3">
      <button class="p-2 border mx-2" @click="sortByDefault">Default</button>
      <button class="p-2 border mx-2" @click="sortByAscending">Ascending</button>
      <button class="p-2 border mx-2" @click="sortByDescending">Descending</button>
    </div>
  </div>

  <div class="flex items-center gap-4">
    <h3 class="font-medium m-0">Contact list</h3>

    <AppButton @click="createNewContact">
      <template #icon>
        <IconPlus class="w-5 h-5" />
      </template>
      Add Contact
    </AppButton>
  </div>

  <div class="grid-cols-[repeat(auto-fill,_minmax(320px,_1fr))] grid gap-5 my-5">
    <ContactItem
      v-for="contact in filteredContacts || contacts"
      :key="contact.id"
      class="cursor-pointer"
      :contact="contact"
      @click="editContact(contact.id)"
      @delete="deleteContact"
      @save="updateContact"
    />
  </div>
</template>
<script lang="ts" setup>

import { ref, watch } from 'vue'
import { useRouter } from 'vue-router'
import { storeToRefs } from 'pinia'
import { useContactsStore } from '@/store'

import ContactItem from '@/components/ContactItem.vue'
import AppButton from '@/components/AppButton.vue'
import IconPlus from '@/components/icons/IconPlus.vue'
import AppInput from '@/components/AppInput.vue'
import MultiSelect from '@/components/MultiSelect.vue'

const router = useRouter()

const contactsStore = useContactsStore()
const { contacts } = storeToRefs(contactsStore)
const { updateContact, deleteContact } = contactsStore

const searchName = ref('')
const filteredContacts = ref()
const multi = ref('')

function filterByNameAndDescr () {
  filteredContacts.value = contacts.value.filter(el => {
    return [el.name, el.description].some(el => el.toLowerCase().startsWith(searchName.value.toLowerCase())) ||
    `${el.name} ${el.description}`.split(' ').some(el => el.toLowerCase().startsWith(searchName.value.toLowerCase()))
  })
}
function filterByRole () {
  filteredContacts.value = contacts.value.filter(el => {
    return el.role === multi.value
  })
}

watch(searchName, () => {
  filterByNameAndDescr()
})
watch(multi, () => {
  filterByRole()
})

function createNewContact () {
  router.push({ name: 'upsertContact', params: { contactId: 'new' } })
}

function editContact (contactId: number) {
  router.push({ name: 'upsertContact', params: { contactId } })
}

function defineChoice (choice: string) {
  multi.value = choice
}

function sortByAscending () {
  filteredContacts.value = contacts.value.sort((a, b) => (a.name > b.name) ? 1 : ((b.name > a.name) ? -1 : 0))
}

function sortByDescending () {
  filteredContacts.value = contacts.value.sort((a, b) => (b.name > a.name) ? 1 : ((a.name > b.name) ? -1 : 0))
}

function sortByDefault () {
  filteredContacts.value = contacts.value.sort((a, b) => (a.id > b.id) ? 1 : ((b.id > a.id) ? -1 : 0))
}
</script>
