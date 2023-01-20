<script setup>
import { useUserListStore } from '@/views/apps/user/useUserListStore'
import { avatarText } from '@core/utils/formatters'
import axios from '@axios'

const userListStore = useUserListStore()
const searchQuery = ref('')
const patientData = ref()
const selectedRole = ref()
const selectedPlan = ref()
const selectedStatus = ref()
const rowPerPage = ref(10)
const currentPage = ref(1)
const totalPage = ref(1)
const totalUsers = ref(0)
const users = ref([])

const faqSearchQuery = ref('')
const faqs = ref([])

const fetchFaqs = () => {
  return axios.get('/pages/faqs', { params: { q: faqSearchQuery.value } }).then(response => {
    faqs.value = response.data
  }).catch(error => {
    console.error(error)
  })
}

const activeTab = ref('Dashboard')
const activeQuestion = ref(0)

watch(activeTab, () => activeQuestion.value = 0)
watch(faqSearchQuery, fetchFaqs, { immediate: true })

const isDialogVisible = ref(false)

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
    title: 'Pending',
    value: 'pending',
  },
  {
    title: 'Approved',
    value: 'approved',
  },
  {
    title: 'Missed',
    value: 'missed',
  },
  {
    title: 'Cancelled',
    value: 'cancelled',
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
    <VCard title="Search Filter">
      <!-- 👉 Filters -->
      <VCardText>
        <VRow>
          <!-- 👉 Select ID -->
          <VCol
            cols="12"
            sm="6"
          >
            <VTextField
              v-model="patientData"
              label="Search"
              placeholder="Search for patient: ID, name, phone number, email"
              clearable
            />
          </VCol>
          <!-- 👉 Select Status -->
          <VCol
            cols="12"
            sm="3"
          >
            <VSelect
              v-model="selectedStatus"
              label="Select Status"
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
              USER
            </th>
            <th scope="col">
              ROLE
            </th>
            <th scope="col">
              PLAN
            </th>
            <th scope="col">
              BILLING
            </th>
            <th scope="col">
              STATUS
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
            <!-- 👉 User -->
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
                    <VDialog
                      v-model="isDialogVisible"
                      fullscreen
                      :scrim="false"
                      transition="dialog-bottom-transition"
                    >
                      <!-- Dialog Activator -->
                      <template #activator="{ props }">
                        <VBtn 
                          v-bind="props"
                          variant="text"
                        >
                          {{ user.fullName }}
                        </VBtn>
                      </template>

                      <!-- Dialog Content -->
                      <VCard>
                        <!-- Toolbar -->
                        <div>
                          <VToolbar color="primary">
                            <VSpacer />

                            <VToolbarItems>
                              <VBtn
                                icon
                                variant="plain"
                                @click="isDialogVisible = false"
                              >
                                <VIcon
                                  color="white"
                                  icon="tabler-x"
                                />
                              </VBtn>
                            </VToolbarItems>
                          </VToolbar>
                        </div>
                        
                        <!-- 👉 Faq sections and questions -->
                        <VRow class="v-row-modal">
                          <VCol
                            v-show="faqs.length"
                            cols="12"
                            sm="4"
                            lg="3"
                            class="position-relative"
                          >
                            <!-- 👉 Tabs -->
                            <VTabs
                              v-model="activeTab"
                              direction="vertical"
                              class="v-tabs-pill"
                              grow
                            >
                              <VTab
                                v-for="faq in faqs"
                                :key="faq.faqTitle"
                                :value="faq.faqTitle"
                                class="text-high-emphasis"
                              >
                                <VIcon
                                  :icon="faq.faqIcon"
                                  :size="20"
                                  start
                                />
                                {{ faq.faqTitle }}
                              </VTab>
                            </VTabs>
                          </VCol>

                          <VCol
                            cols="12"
                            sm="8"
                            lg="9"
                          >
                            <!-- 👉 Windows -->
                            <VWindow
                              v-model="activeTab"
                              class="faq-v-window disable-tab-transition"
                            >
                              <VWindowItem
                                v-for="faq in faqs"
                                :key="faq.faqTitle"
                                :value="faq.faqTitle"
                              >
                                <div class="d-flex align-center mb-6">
                                  <VAvatar
                                    rounded
                                    color="primary"
                                    variant="tonal"
                                    class="me-3"
                                    size="large"
                                  >
                                    <VIcon
                                      :size="32"
                                      :icon="faq.faqIcon"
                                    />
                                  </VAvatar>

                                  <div>
                                    <h6 class="text-h6">
                                      {{ faq.faqTitle }}
                                    </h6>
                                    <span class="text-sm">{{ faq.faqSubtitle }}</span>
                                  </div>
                                </div>

                                <VExpansionPanels
                                  v-model="activeQuestion"
                                  multiple
                                >
                                  <VExpansionPanel
                                    v-for="item in faq.faqs"
                                    :key="item.question"
                                    :title="item.question"
                                    :text="item.answer"
                                  />
                                </VExpansionPanels>
                              </VWindowItem>
                            </VWindow>
                          </VCol>

                          <VCol
                            v-show="!faqs.length"
                            cols="12"
                            :class="!faqs.length ? 'd-flex justify-center align-center' : ''"
                          >
                            <VIcon
                              icon="tabler-help"
                              start
                              size="20"
                            />
                            <span class="text-base font-weight-medium">
                              No Results Found!!
                            </span>
                          </VCol>
                        </VRow>
                      </VCard>
                    </VDialog>
                  </h6>
                  <span class="text-sm text-disabled">@{{ user.email }}</span>
                </div>
              </div>
            </td>

            <!-- 👉 Role -->
            <td>
              <VAvatar
                :color="resolveUserRoleVariant(user.role).color"
                :icon="resolveUserRoleVariant(user.role).icon"
                variant="tonal"
                size="30"
                class="me-4"
              />
              <span class="text-capitalize text-base">{{ user.role }}</span>
            </td>

            <!-- 👉 Plan -->
            <td>
              <span class="text-capitalize text-base font-weight-semibold">{{ user.currentPlan }}</span>
            </td>

            <!-- 👉 Billing -->
            <td>
              <span class="text-base">{{ user.billing }}</span>
            </td>

            <!-- 👉 Status -->
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
