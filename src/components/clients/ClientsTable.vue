<script setup>
import { useClientStore } from '@/stores/clients'
import { reactive, ref, computed } from 'vue'
import LeBtn from '@/components/UI/LeBtn.vue'
import LeTd from '../UI/table/LeTd.vue'
import LeInput from '../UI/LeInput.vue'
import LeTh from '../UI/table/LeTh.vue'
import {
  PencilIcon,
  TrashIcon,
  CheckCircleIcon,
  XCircleIcon,
  UserPlusIcon,
  MagnifyingGlassIcon,
} from '@heroicons/vue/24/outline'

const clientStore = useClientStore()
const editing = ref(false)
// resets client inputs
const resetInputs = (formName) => {
  formName.name = ''
  formName.company = ''
  formName.email = ''
  formName.address = ''
}
const clientForm = reactive({ name: '', company: '', email: '', address: '' })
const editForm = reactive({ id: null, name: '', company: '', email: '', address: '' })

function addNewClient() {
  clientStore.createNew(clientForm)
  resetInputs(clientForm)
}

const editedRowId = ref(null)
// Used to restore initial values on cancel
// const editCache = ref({})
function editTrue(client) {
  editedRowId.value = client.id
  Object.assign(editForm, client)
  editing.value = true
}
function editSave() {
  clientStore.update(editForm)
  // validation
  editing.value = false
  resetInputs(editForm)
}
function cancelEdit() {
  // Reset edit state
  editing.value = false
  editedRowId.value = null
  resetInputs(editForm)
}
function removeClient(client) {
  clientStore.del(client)
}
// Search
const searchQuery = ref('')

const filteredClients = computed(() => {
  return clientStore.clients.filter((c) => {
    return c.name.trim().toLowerCase().includes(searchQuery.value.toLowerCase())
  })
})
</script>
<template>
  <div class="flow-root">
    <div class="align-middle">
      <div class="inline-block min-w-full">
        <!-- Search -->
        <div class="mx-auto w-full max-w-[80%] 2xl:max-w-[70%]">
          <div
            class="ring-t ring-x bg-sec -mb-2 w-fit rounded-t-lg px-2 pt-2 pb-4 ring-1 ring-neutral-400 dark:ring-neutral-600"
          >
            <div class="flex items-center">
              <MagnifyingGlassIcon class="fixed ml-2 size-4.5"></MagnifyingGlassIcon>
              <input
                id="srchQry-clients-1"
                v-model="searchQuery"
                type="text"
                placeholder="Search by name..."
                class="input pl-8"
              />
            </div>
          </div>
        </div>
        <div
          class="mx-auto max-h-[80vh] max-w-[80%] overflow-y-auto shadow-lg ring-1 ring-neutral-400 sm:rounded-lg 2xl:max-w-[70%] dark:ring-neutral-600"
        >
          <table class="min-w-full">
            <thead
              class="bg-sec text-fg sticky top-0 z-10 border-b px-3 text-left text-sm font-semibold backdrop-blur-sm backdrop-filter"
            >
              <tr>
                <th scope="col" class="py-3.5 pr-3 pl-4 text-left text-sm font-semibold sm:pl-4">
                  Name
                </th>
                <th scope="col" class="px-3 py-3.5 text-left text-sm font-semibold">Company</th>
                <th scope="col" class="px-3 py-3.5 text-left text-sm font-semibold">Email</th>
                <th scope="col" class="px-3 py-3.5 text-left text-sm font-semibold">Address</th>
                <th scope="col" class="relative py-3.5 pr-4 pl-3 text-center sm:pr-6">Action</th>
              </tr>
              <tr class="bg-sec">
                <LeTh>
                  <LeInput
                    label=""
                    id="new-client-name-id-1"
                    name="client-name"
                    type="text"
                    autocomplete="name"
                    placeholder="Client name"
                    required
                    v-model="clientForm.name"
                  />
                </LeTh>
                <LeTh>
                  <LeInput
                    label=""
                    id="new-client-company-1"
                    name="client-company"
                    type="text"
                    placeholder="Company name"
                    autocomplete="organization"
                    required
                    v-model="clientForm.company"
                  />
                </LeTh>
                <LeTh>
                  <LeInput
                    label=""
                    id="new-client-email-1"
                    name="client-email"
                    type="text"
                    placeholder="Email"
                    autocomplete="email"
                    required
                    v-model="clientForm.email"
                  />
                </LeTh>
                <LeTh>
                  <LeInput
                    label=""
                    id="new-client-address-1"
                    name="client-address"
                    type="text"
                    placeholder="Address"
                    autocomplete="address"
                    required
                    v-model="clientForm.address"
                  />
                </LeTh>
                <LeTh class="">
                  <div class="mx-1 flex w-full justify-center">
                    <LeBtn
                      @click="
                        (addNewClient(clientForm), console.log('add new in template clicked '))
                      "
                      buttonText=""
                    >
                      <div class="flex items-center gap-2">
                        <UserPlusIcon class="size-5"></UserPlusIcon>
                        Add New
                      </div>
                    </LeBtn>
                  </div>
                </LeTh>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(client, index) in filteredClients" :key="index">
                <!-- Editable Row -->
                <template v-if="editing && editedRowId === client.id">
                  <LeTd>
                    <LeInput
                      :id="`edit-client-name-${client.id}`"
                      :placeholder="client.name"
                      v-model="editForm.name"
                    />
                  </LeTd>
                  <LeTd
                    ><LeInput
                      :id="`edit-client-company-${client.id}`"
                      :placeholder="client.company"
                      v-model="editForm.company"
                  /></LeTd>
                  <LeTd
                    ><LeInput
                      :id="`edit-client-email-${client.id}`"
                      :placeholder="client.email"
                      v-model="editForm.email"
                  /></LeTd>
                  <LeTd
                    ><LeInput
                      :id="`edit-client-address-${client.id}`"
                      :placeholder="client.address"
                      v-model="editForm.address"
                  /></LeTd>
                  <LeTd>
                    <div class="flex w-full items-center justify-center gap-5 py-4 text-center">
                      <a href="#" class="text-success hover:brightness-110" @click="editSave()"
                        ><CheckCircleIcon class="size-5"></CheckCircleIcon
                      ></a>
                      <a href="#" class="text-danger hover:brightness-110" @click="cancelEdit()"
                        ><XCircleIcon class="size-5"></XCircleIcon
                      ></a>
                    </div>
                  </LeTd>
                </template>
                <!-- Read-Only Row -->
                <template v-else>
                  <LeTd>{{ client.name }}</LeTd>
                  <LeTd>{{ client.company }}</LeTd>
                  <LeTd>{{ client.email }}</LeTd>
                  <LeTd class="text-xs"> {{ client.address }}</LeTd>
                  <LeTd>
                    <!-- Controls pading for entire read only row -->
                    <div class="flex w-full items-center justify-center gap-5 py-3 text-center">
                      <a href="#" @click="editTrue(client)">
                        <PencilIcon class="hover:text-success size-5"></PencilIcon>
                      </a>
                      <a href="#" class="hover:text-danger" @click="removeClient(client)">
                        <TrashIcon class="size-5"></TrashIcon>
                      </a>
                    </div>
                  </LeTd>
                </template>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
