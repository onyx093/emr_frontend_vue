<script setup>
import { useUserListStore } from '@/views/apps/user/useUserListStore'
import AppDateTimePicker from '@core/components/AppDateTimePicker.vue'
import { avatarText } from '@core/utils/formatters'

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

const patientData = ref('')

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
          <!-- 👉 Select ID -->
          <VCol
            cols="12"
            sm="5"
          >
            <VTextField
              v-model="patientData"
              label="Search"
              placeholder="Search for items"
              clearable
            />
          </VCol>
          <VCol
            cols="12"
            sm="2"
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
              NAME
            </th>
            <th scope="col">
              QUANTITY
            </th>
            <th scope="col">
              UNIT OF MEASURE
            </th>
            <th scope="col">
              UNIT COST
            </th>
            <th scope="col">
              SELLING COST
            </th>
            <th scope="col">
              STOCK LEVEL
            </th>
            <th scope="col">
              ACTIONS
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
            <!-- 👉 Name -->
            <td>
              <span class="text-base">10ML SYRINGE</span>
            </td>

            <!-- 👉 Quantity -->
            <td>
              <span class="text-base">-137</span>
            </td>

            <!-- 👉 Unit Of Measure -->
            <td>
              <span class="text-base">PACKS</span>
            </td>

            <!-- 👉 Unit Cost -->
            <td>
              <span class="text-base">&#x20A6;4,000.00</span>
            </td>

            <!-- 👉 Selling Cost -->
            <td>
              <span class="text-base">&#x20A6;4,000.00</span>
            </td>

            <!-- 👉 Stock Level -->
            <td>
              <VChip
                label
                :color="resolveUserStatusVariant(user.status)"
                size="small"
                class="text-capitalize"
              >
                low
              </VChip>
            </td>

            <!-- 👉 Actions -->
            <td
              class="text-center"
              style="width: 5rem;"
            >
              <VBtn
                icon
                size="x-small"
                color="default"
                variant="text"
              >
                <VIcon
                  size="22"
                  icon="tabler-edit"
                />
              </VBtn>

              <VBtn
                icon
                size="x-small"
                color="default"
                variant="text"
              >
                <VIcon
                  size="22"
                  icon="tabler-circle-x"
                />
              </VBtn>
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
