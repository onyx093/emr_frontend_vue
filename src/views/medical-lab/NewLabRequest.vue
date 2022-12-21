<script setup>
const patientName = ref('')
const labGroup = ref('')
const labTest = ref()
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

      <VCol cols="6">
        <VTextField
          v-model="labGroup"
          label="Lab Group"
          placeholder="Select Lab Group"
        />
      </VCol>

      <VCol cols="6">
        <VTextField
          v-model="labTest"
          label="Lab Test"
          placeholder="Select Lab Test"
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
