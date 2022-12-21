<script setup>
const patientName = ref('')
const radiologyTest = ref('')
const requestNote = ref()
const urgent = ref(false)
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

      <VCol cols="12">
        <VTextField
          v-model="radiologyTest"
          label="Radiology Test"
          placeholder="Select Radiology Test"
        />
      </VCol>

      <VCol cols="12">
        <VTextarea
          v-model="requestNote"
          label="Request note"
          auto-grow
        />
      </VCol>

      <VCol cols="12">
        <VCheckbox
          v-model="urgent"
          label="Please check if urgent"
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
