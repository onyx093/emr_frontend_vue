<!-- eslint-disable import/newline-after-import -->
<script setup>
import AllPatientList from '@/views/frontdesk/AllPatientList.vue'
import CurrentPatientList from '@/views/frontdesk/CurrentPatientList.vue'

const currentTab = ref(0)
const isNewPatientDialogVisible = ref(false)
const isNewAppointmentDialogVisible = ref(false)
const tabs = ["Today's Appointments", "All Appointments"]

const tabItemContent = [CurrentPatientList, AllPatientList]
</script>

<template>
  <VRow class="match-height">
    <VCol
      cols="12"
      md="12"
    >
      <VTabs
        v-model="currentTab"
        class="h6"
      >
        <VTab
          v-for="tab, key in tabs"
          :key="key"
        >
          {{ tab }}
        </VTab>

        
        <div class="w-100 d-flex align-items-end">
          <VSpacer />
          <VDialog
            v-model="isNewPatientDialogVisible"
            max-width="960"
          >
            <!-- Dialog Activator -->
            <template #activator="{ props }">
              <VBtn
                v-bind="props"
                color="primary"
                size="small"
                class="mr-4"
              >
                New Patient
              </VBtn>
            </template>
  
            <!-- Dialog close btn -->
            <DialogCloseBtn @click="isNewPatientDialogVisible = !isNewPatientDialogVisible" />
  
            <!-- Dialog Content -->
            <VCard title="New Patient Registration">
              <VCardText>
                <VRow>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="surname"
                      label="Surname"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="otherNames"
                      label="Other Names"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="email"
                      label="Email Address"
                      persistent-hint
                    />
                  </VCol>
                  <VCol cols="12">
                    <VTextField
                      v-model="email"
                      label="Email"
                    />
                  </VCol>
                  <VCol cols="12">
                    <VTextField
                      v-model="password"
                      label="Password"
                      type="password"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                  >
                    <VSelect
                      v-model="age"
                      :items="['0-17', '18-29', '30-54', '54+']"
                      label="Age"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                  >
                    <VAutocomplete
                      v-model="interest"
                      multiple
                      :items="['Skiing', 'Ice hockey', 'Soccer', 'Basketball', 'Hockey', 'Reading', 'Writing', 'Coding', 'Basejump']"
                      label="Interests"
                    />
                  </VCol>
                </VRow>
              </VCardText>
  
              <VCardText class="d-flex justify-end flex-wrap gap-3">
                <VBtn
                  variant="tonal"
                  color="secondary"
                  @click="isNewPatientDialogVisible = !isNewPatientDialogVisible"
                >
                  Close
                </VBtn>
                <VBtn @click="isNewPatientDialogVisible = !isNewPatientDialogVisible">
                  Save
                </VBtn>
              </VCardText>
            </VCard>
          </VDialog>
  
          <VDialog
            v-model="isNewAppointmentDialogVisible"
            max-width="600"
          >
            <!-- Dialog Activator -->
            <template #activator="{ props }">
              <VBtn
                v-bind="props"
                color="primary"
                size="small"
              >
                New Appointment
              </VBtn>
            </template>
  
            <!-- Dialog close btn -->
            <DialogCloseBtn @click="isNewAppointmentDialogVisible = !isNewAppointmentDialogVisible" />
  
            <!-- Dialog Content -->
            <VCard title="New Patient Registration">
              <VCardText>
                <VRow>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="firstName"
                      label="First Name"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="middleName"
                      label="Middle Name"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <VTextField
                      v-model="lastName"
                      label="Last Name"
                      persistent-hint
                    />
                  </VCol>
                  <VCol cols="12">
                    <VTextField
                      v-model="email"
                      label="Email"
                    />
                  </VCol>
                  <VCol cols="12">
                    <VTextField
                      v-model="password"
                      label="Password"
                      type="password"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                  >
                    <VSelect
                      v-model="age"
                      :items="['0-17', '18-29', '30-54', '54+']"
                      label="Age"
                    />
                  </VCol>
                  <VCol
                    cols="12"
                    sm="6"
                  >
                    <VAutocomplete
                      v-model="interest"
                      multiple
                      :items="['Skiing', 'Ice hockey', 'Soccer', 'Basketball', 'Hockey', 'Reading', 'Writing', 'Coding', 'Basejump']"
                      label="Interests"
                    />
                  </VCol>
                </VRow>
              </VCardText>
  
              <VCardText class="d-flex justify-end flex-wrap gap-3">
                <VBtn
                  variant="tonal"
                  color="secondary"
                  @click="isNewAppointmentDialogVisible = !isNewAppointmentDialogVisible"
                >
                  Close
                </VBtn>
                <VBtn @click="isNewAppointmentDialogVisible = !isNewAppointmentDialogVisible">
                  Save
                </VBtn>
              </VCardText>
            </VCard>
          </VDialog>
        </div>
      </VTabs>
      <VDivider />

      <VWindow
        v-model="currentTab"
        class="mt-5"
      >
        <VWindowItem
          v-for="(item, key) in tabItemContent"
          :key="key"
        >
          <Component :is="item" />
        </VWindowItem>
      </VWindow>
    </VCol>
  </VRow>
</template>
