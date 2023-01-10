<script setup>
import { useUserListStore } from '@/views/apps/user/useUserListStore'
import { avatarText } from '@core/utils/formatters'
import AppDateTimePicker from '@core/components/AppDateTimePicker.vue'

const userListStore = useUserListStore()
const searchQuery = ref('')
const selectedRole = ref()
const selectedPlan = ref()
const selectedStatus = ref()
const rowPerPage = ref(10)
const currentPage = ref(1)
const totalPage = ref(1)
const totalUsers = ref(0)
const users = ref([])

const dateRange = ref('')

// 👉 Fetching users
const fetchUsers = () => {
  userListStore.fetchUsers({
    q: searchQuery.value,
    status: selectedStatus.value,
    plan: selectedPlan.value,
    role: selectedRole.value,
    perPage: rowPerPage.value,
    currentPage: currentPage.value,
  }).then(response => {
    users.value = response.data.users
    totalPage.value = response.data.totalPage
    totalUsers.value = response.data.totalUsers
  }).catch(error => {
    console.error(error)
  })
}

watchEffect(fetchUsers)

const status = [
  {
    title: 'All',
    value: 'all',
  },
  {
    title: 'Enabled',
    value: 'enabled',
  },
  {
    title: 'Disabled',
    value: 'disabled',
  },
]

const resolveUserRoleVariant = role => {
  if (role === 'subscriber')
    return {
      color: 'warning',
      icon: 'tabler-user',
    }
  if (role === 'author')
    return {
      color: 'success',
      icon: 'tabler-circle-check',
    }
  if (role === 'maintainer')
    return {
      color: 'primary',
      icon: 'tabler-chart-pie-2',
    }
  if (role === 'editor')
    return {
      color: 'info',
      icon: 'tabler-pencil',
    }
  if (role === 'admin')
    return {
      color: 'secondary',
      icon: 'tabler-device-laptop',
    }
  
  return {
    color: 'primary',
    icon: 'tabler-user',
  }
}

const resolveUserStatusVariant = stat => {
  if (stat === 'cancelled')
    return 'warning'
  if (stat === 'completed')
    return 'success'
  if (stat === 'seeing doctor')
    return 'secondary'
  
  return 'primary'
}

// 👉 watching current page
watchEffect(() => {
  if (currentPage.value > totalPage.value)
    currentPage.value = totalPage.value
})

// 👉 watching current page
watchEffect(() => {
  if (currentPage.value > totalPage.value)
    currentPage.value = totalPage.value
})

// 👉 Computing pagination data
const paginationData = computed(() => {
  const firstIndex = users.value.length ? (currentPage.value - 1) * rowPerPage.value + 1 : 0
  const lastIndex = users.value.length + (currentPage.value - 1) * rowPerPage.value
  
  return `Showing ${ firstIndex } to ${ lastIndex } of ${ totalUsers.value } entries`
})
</script>

<template>
  <VCol cols="12">
    <VCard>
      <!-- 👉 Filters -->
      <VCardText>
        <VRow>
          <!-- 👉 Select Date Range -->
          <VCol
            cols="12"
            sm="3"
          >
            <AppDateTimePicker
              v-model="dateRange"
              label="Date Range"
              :config="{ mode: 'range' }"
            />
          </VCol>
          <!-- 👉 Select ID -->
          <VCol
            cols="12"
            sm="3"
          >
            <VTextField
              v-model="patientData"
              label="Search"
              placeholder="Search for patient: ID, name, phone number, email"
              clearable
            />
          </VCol>
          <VCol
            cols="12"
            sm="3"
          >
            <VSelect
              v-model="selectedStatus"
              label="Select HMO"
              :items="status"
              clearable
              clear-icon="tabler-x"
            />
          </VCol>
          <VCol
            cols="12"
            sm="3"
          >
            <VBtn
              prepend-icon="tabler-search"
              @click="isAddNewUserDrawerVisible = true"
            >
              Filter
            </VBtn>
          </VCol>
        </VRow>
      </VCardText>

      <VDivider />

      <VTable class="text-no-wrap">
        <!-- 👉 table head -->
        <thead>
          <tr>
            <th scope="col">
              PATIENT NAME
            </th>
            <th scope="col">
              PATIENT ID
            </th>
            <th scope="col">
              REQUEST DATE
            </th>
            <th scope="col">
              FULL DATE
            </th>
            <th scope="col">
              DRUG ITEM
            </th>
            <th scope="col">
              AMOUNT
            </th>
            <th scope="col">
              QUANTITY
            </th>
          </tr>
        </thead>
        <!-- 👉 table body -->
        <tbody>
          <tr
            v-for="user in users"
            :key="user.id"
            style="height: 3.75rem;"
          >
            <!-- 👉 Patient Name -->
            <td>
              <span class="text-base">Mtsor Yemisi Grace</span>
            </td>

            <!-- 👉 Patient ID -->
            <td>
              <span class="text-base">14525</span>
            </td>

            <!-- 👉 Request Date -->
            <td>
              <span class="text-base">{{ new Date().toLocaleString() }}</span>
            </td>

            <!-- 👉 Fill Date -->
            <td>
              <span class="text-base">{{ new Date().toLocaleString() }}</span>
            </td>

            <!-- 👉 Drug Item -->
            <td>
              <span class="text-base">Doxycycline Capsules (Card)</span>
            </td>

            <!-- 👉 Amount -->
            <td>
              <span class="text-base">&#x20A6;200</span>
            </td>

            <!-- 👉 Quantity -->
            <td>
              <span class="text-base">4</span>
            </td>
          </tr>
        </tbody>

        <!-- 👉 table footer  -->
        <tfoot v-show="!users.length">
          <tr>
            <td
              colspan="7"
              class="text-center"
            >
              No data available
            </td>
          </tr>
        </tfoot>
      </VTable>

      <VDivider />

      <VCardText class="d-flex align-center flex-wrap justify-space-between gap-4 py-3 px-5">
        <span class="text-sm text-disabled">
          {{ paginationData }}
        </span>

        <VPagination
          v-model="currentPage"
          size="small"
          :total-visible="5"
          :length="totalPage"
        />
      </VCardText>
    </VCard>
  </VCol>
</template>
