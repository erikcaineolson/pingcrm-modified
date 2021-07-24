<template>
  <div>
    <h1 class="mb-8 font-bold text-3xl">Contacts</h1>
    <div class="mb-6 flex justify-between items-center">
      <search-filter v-model="form.search" class="w-full max-w-md mr-4" @reset="reset">
        <label class="block text-gray-700">Trashed:</label>
        <select v-model="form.trashed" class="mt-1 w-full form-select">
          <option :value="null"/>
          <option value="with">With Trashed</option>
          <option value="only">Only Trashed</option>
        </select>
      </search-filter>
      <a class="btn-indigo" style="cursor: pointer" @click="showCreateModal">
        <span>Create</span>
        <span class="hidden md:inline">Contact</span>
      </a>
    </div>
    <div class="bg-white rounded-md shadow overflow-x-auto">
      <table class="w-full whitespace-nowrap">
        <tr class="text-left font-bold">
          <th class="px-6 pt-6 pb-4">Name</th>
          <th class="px-6 pt-6 pb-4">Organization</th>
          <th class="px-6 pt-6 pb-4">City</th>
          <th class="px-6 pt-6 pb-4" colspan="2">Phone</th>
        </tr>
        <tr v-for="contact in contacts.data" :key="contact.id" class="hover:bg-gray-100 focus-within:bg-gray-100">
          <td class="border-t">
            <a class="px-6 py-4 flex items-center focus:text-indigo-500" @click="showEditModal(contact.id)">
              {{ contact.name }}
              <icon v-if="contact.deleted_at" name="trash" class="flex-shrink-0 w-3 h-3 fill-gray-400 ml-2"/>
            </a>
          </td>
          <td class="border-t">
            <a class="px-6 py-4 flex items-center" @click="showEditModal(contact)" tabindex="-1">
              <div v-if="contact.organization">
                {{ contact.organization.name }}
              </div>
            </a>
          </td>
          <td class="border-t">
            <a class="px-6 py-4 flex items-center" @click="showEditModal(contact)" tabindex="-1">
              {{ contact.city }}
            </a>
          </td>
          <td class="border-t">
            <a class="px-6 py-4 flex items-center" @click="showEditModal(contact)" tabindex="-1">
              {{ contact.phone }}
            </a>
          </td>
          <td class="border-t w-px">
            <a class="px-4 flex items-center" @click="showEditModal(contact)" tabindex="-1">
              <icon name="cheveron-right" class="block w-6 h-6 fill-gray-400"/>
            </a>
          </td>
        </tr>
        <tr v-if="contacts.data.length === 0">
          <td class="border-t px-6 py-4" colspan="4">No contacts found.</td>
        </tr>
      </table>
    </div>
    <pagination class="mt-6" :links="contacts.links"/>
    <div v-if="createForm">
      <Create />
    </div>
    <div v-if="editForm">
      <Edit :contact="contact" />
    </div>
  </div>
</template>

<script>
import Edit from './Edit'
import Icon from '@/Shared/Icon'
import Create from './Create'
import pickBy from 'lodash/pickBy'
import Layout from '@/Shared/Layout'
import throttle from 'lodash/throttle'
import mapValues from 'lodash/mapValues'
import Pagination from '@/Shared/Pagination'
import SearchFilter from '@/Shared/SearchFilter'

export default {
  metaInfo: {title: 'Contacts'},
  components: {
    Edit,
    Icon,
    Create,
    Pagination,
    SearchFilter,
  },
  layout: Layout,
  props: {
    filters: Object,
    contacts: Object,
  },
  data() {
    return {
      contact: null,
      createForm: false,
      editForm: false,
      form: {
        search: this.filters.search,
        trashed: this.filters.trashed,
      },
    }
  },
  watch: {
    form: {
      deep: true,
      handler: throttle(function () {
        this.$inertia.get(this.route('contacts'), pickBy(this.form), {preserveState: true})
      }, 150),
    },
  },
  methods: {
    hideCreateModal() {
      this.createForm = false;
    },
    hideEditModal() {
      this.editForm = false;
    },
    showCreateModal() {
      this.hideEditModal();
      this.createForm = true;
    },
    showEditModal(contact) {
      this.hideCreateModal();
      this.contact = contact;
      this.editForm = true;
    },
    reset() {
      this.form = mapValues(this.form, () => null)
    },
  },
}
</script>
