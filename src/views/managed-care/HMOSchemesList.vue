<script setup>
import { useUserListStore } from '@/views/apps/user/useUserListStore'
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
      <VTable class="text-no-wrap">
        <!-- 👉 table head -->
        <thead>
          <tr>
            <th scope="col">
              S/N
            </th>
            <th scope="col">
              HMO COMPANY
            </th>
            <th scope="col">
              SCHEME
            </th>
            <th scope="col">
              TYPE
            </th>
            <th scope="col">
              PHONE
            </th>
            <th scope="col">
              EMAIL
            </th>
            <th scope="col">
              CAC NUMBER
            </th>
            <th scope="col">
              COVERAGE
            </th>
            <th scope="col">
              PATIENT
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
            <!-- 👉 S/N -->
            <td>
              <span class="text-base">{{ user.id }}</span>
            </td>

            <!-- 👉 HMO Company -->
            <td>
              <span class="text-base">Deda Hospital</span>
            </td>

            <!-- 👉 Scheme -->
            <td>
              <span class="text-base">GNI2</span>
            </td>

            <!-- 👉 Type -->
            <td>
              <span class="text-base">PHIS</span>
            </td>

            <!-- 👉 Phone -->
            <td>
              <span class="text-base">08067340912</span>
            </td>

            <!-- 👉 Email -->
            <td>
              <span class="text-base">abc@gmail.com</span>
            </td>

            <!-- 👉 CAC Number -->
            <td>
              <span class="text-base">--</span>
            </td>

            <!-- 👉 Coverage -->
            <td>
              <span class="text-base">Full</span>
            </td>

            <!-- 👉 Patients -->
            <td>
              <span class="text-base">0 patients</span>
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
