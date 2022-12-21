<script setup>
const patientName = ref('')
const selectedProcedureType = ref('')
const selectedProcedure = ref()
const requestNote = ref('')

const procedureTypes = [
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
</script>

<template>
  <VForm @submit.prevent="{}">
    <VRow class="pt-4">
      <VCol cols="12">
        <VTextField
          v-model="patientName"
          label="Patient Name"
          placeholder="Search Patient"
        />
      </VCol>

      <VCol cols="6">
        <VSelect
          v-model="selectedProcedureType"
          label="Select Procedure Type"
          :items="procedureTypes"
          clearable
          clear-icon="tabler-x"
        />
      </VCol>

      <VCol cols="6">
        <VTextField
          v-model="selectedProcedure"
          label="Procedure"
          placeholder="Select Procedure"
        />
      </VCol>

      <VCol cols="12">
        <VTextField
          v-model="patientName"
          label="Primary Diagnoses"
          placeholder="Select Primary Diagnosis"
        />
      </VCol>

      <VCol cols="12">
        <VTextarea
          v-model="requestNote"
          label="Request note"
          auto-grow
        />
      </VCol>

      <VCol cols="6">
        <VRadioGroup
          v-model="hasOutsidePrescription"
          inline
        >
          <div>
            <VRadio
              label="Pay Now"
              value="Yes"
            />
          </div>
          <div>
            <VRadio
              label="Pay Later"
              value="No"
            />
          </div>
        </VRadioGroup>
      </VCol>

      <VCol cols="12">
        <VBtn
          type="submit"
          class="me-2"
        >
          Send Request
        </VBtn>
      </VCol>
    </VRow>
  </VForm>
</template>
