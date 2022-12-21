<!-- eslint-disable import/newline-after-import -->
<script setup>
import EnrolledAntenatalPatientList from '@/views/doctor/EnrolledAntenatalPatientList.vue'

const currentTab = ref(0)
const tabs = ["Labouor Management(Dashboard)"]

const tabItemContent = [EnrolledAntenatalPatientList]
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
