<script setup>
import PrescriptionQueueList from '@/views/pharmacy/PrescriptionQueueList.vue'
import PrescriptionRequestList from '@/views/pharmacy/PrescriptionRequestList.vue'

const currentTab = ref(0)
const tabs = ["Prescription Queue", "Prescription Requests"]

const tabItemContent = [PrescriptionQueueList, PrescriptionRequestList]
</script>

<template>
  <VRow class="match-height">
    <VCol
      cols="12"
      md="12"
    >
      <VTabs v-model="currentTab">
        <VTab
          v-for="tab, key in tabs"
          :key="key"
        >
          {{ tab }}
        </VTab>
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
