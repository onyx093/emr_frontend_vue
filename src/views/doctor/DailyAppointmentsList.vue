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
              DATE
            </th>
            <th scope="col">
              PATIENT
            </th>
            <th scope="col">
              DEPARTMENT
            </th>
            <th scope="col">
              REASON
            </th>
            <th scope="col">
              ACCEPTED
            </th>
            <th scope="col">
              WHOM TO SEE
            </th>
            <th scope="col">
              WAS SCHEDULED
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
            <!-- 👉 ID -->
            <td>
              <span class="text-base">{{ new Date().toLocaleString() }}</span>
            </td>

            <!-- 👉 Patient -->
            <td>
              <div class="d-flex align-center">
                <VAvatar
                  variant="tonal"
                  :color="resolveUserRoleVariant(user.role).color"
                  class="me-3"
                  size="38"
                >
                  <VImg
                    v-if="user.avatar"
                    :src="user.avatar"
                  />
                  <span v-else>{{ avatarText(user.fullName) }}</span>
                </VAvatar>

                <div class="d-flex flex-column">
                  <h6 class="text-base">
                    <RouterLink
                      :to="{ name: 'apps-user-view-id', params: { id: user.id } }"
                      class="font-weight-medium user-list-name"
                    >
                      {{ user.fullName }}
                    </RouterLink>
                  </h6>
                  <span class="text-sm text-disabled">@{{ user.email }}</span>
                </div>
              </div>
            </td>

            <!-- 👉 Department -->
            <td>
              <span class="text-base">Family Medicine/GOPD (Follow Up)</span>
            </td>

            <!-- 👉 Reason -->
            <td>
              <span class="text-base">For circumcision</span>
            </td>

            <!-- 👉 Accepted -->
            <td>
              <VBtn
                size="small"
                color="primary"
                variant="flat"
                class="mr-2"
              >
                Open Profile
              </VBtn>
              <VIcon
                size="22"
                icon="tabler-volume"
              />
            </td>

            <!-- 👉 Whom To See -->
            <td>
              <span class="text-base">Room 3 (Ikechukwu Anosike)</span>
            </td>

            <!-- 👉 Was Scheduled -->
            <td>
              <VChip
                label
                :color="resolveUserStatusVariant(user.status)"
                size="small"
                class="text-capitalize"
              >
                {{ user.status }}
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
                  icon="tabler-eye"
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
