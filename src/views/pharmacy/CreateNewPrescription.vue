<script setup>
const patientName = ref('')
const genericDrugName = ref('')
const drugName = ref()
const doseQuantity = ref()
const frequency = ref()
const frequencyType = ref()
const duration = ref()
const refillCount = ref()
const regimenNoteInstruction = ref('')
const hasOutsidePrescription = ref(0)
const regimenNote = ref('')

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
        <VTextField
          v-model="genericDrugName"
          label="Drug Generic Name"
          placeholder="Select Generic Name"
        />
      </VCol>

      <VCol cols="6">
        <VTextField
          v-model="drugName"
          label="Drug Name"
          placeholder="Select a Drug"
        />
      </VCol>

      <VCol cols="4">
        <VTextField
          v-model="doseQuantity"
          label="Drug Name"
          placeholder="Select a Drug"
        />
      </VCol>

      <VCol cols="4">
        <VTextField
          v-model="frequency"
          label="Drug Name"
          placeholder="Select a Drug"
        />
      </VCol>

      <VCol
        cols="4"
      >
        <VSelect
          v-model="frequencyType"
          label="Request"
          :items="status"
          clearable
          clear-icon="tabler-x"
        />
      </VCol>

      <VCol cols="4">
        <VTextField
          v-model="duration"
          label="Duration"
          placeholder="Duration"
        />
      </VCol>

      <VCol cols="4">
        <VTextField
          v-model="refillCount"
          label="Number of Refills"
          placeholder="Number of Refills"
          disabled
        />
      </VCol>

      <VCol cols="4">
        <VTextField
          v-model="regimenNoteInstruction"
          label="Note"
          placeholder="Regimen Line Instruction"
        />
      </VCol>

      <VCol cols="12">
        <VTextField
          v-model="diagnosisData"
          label="Diagnosis Data"
          placeholder="Search for Diagnosis"
        />
      </VCol>

      <VCol cols="6">
        <p class="text-subtitle-1 text-truncate mb-0">
          Prescription from another facility.
        </p>
        <VRadioGroup
          v-model="hasOutsidePrescription"
          title="Prescription from another facility"
          inline
        >
          <div>
            <VRadio
              label="Yes"
              value="Yes"
            />
          </div>
          <div>
            <VRadio
              label="No"
              value="No"
            />
          </div>
        </VRadioGroup>
      </VCol>

      <VCol cols="12">
        <VTextarea
          v-model="regimenNote"
          label="Regimen note"
          placeholder="Regimen note"
          auto-grow
        />
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
