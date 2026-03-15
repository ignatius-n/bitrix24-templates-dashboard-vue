<script setup lang="ts">
import type { Mail } from '../types'
import { computed, ref, watch } from 'vue'
import { useFetch, useBreakpoints, breakpointsTailwind } from '@vueuse/core'
import MailOpenIcon from '@bitrix24/b24icons-vue/outline/MailOpenIcon'

const tabItems = [{
  label: 'All',
  value: 'all'
}, {
  label: 'Unread',
  value: 'unread'
}]
const selectedTab = ref('all')

const { data: mails } = useFetch('https://dashboard-template.nuxt.dev/api/mails', { initialData: [] }).json<Mail[]>()

// Filter mails based on the selected tab
const filteredMails = computed(() => {
  if (selectedTab.value === 'unread') {
    return mails.value?.filter(mail => !!mail.unread) ?? []
  }

  return mails.value ?? []
})

const selectedMail = ref<Mail | null>()

const isMailPanelOpen = computed({
  get() {
    return !!selectedMail.value
  },
  set(value: boolean) {
    if (!value) {
      selectedMail.value = null
    }
  }
})

// Reset selected mail if it's not in the filtered mails
watch(filteredMails, () => {
  if (!filteredMails.value.find(mail => mail.id === selectedMail.value?.id)) {
    selectedMail.value = null
  }
})

const breakpoints = useBreakpoints(breakpointsTailwind)
const isMobile = breakpoints.smaller('lg')
</script>

<template>
  <B24DashboardPanel
    id="inbox-1"
    :default-size="320"
    :min-size="400"
    :max-size="480"
    resizable
    class="bg-(--ui-color-bg-content-primary)"
  >
    <!-- @todo: after UI update fix :b24ui -->
    <B24DashboardNavbar
      title="Inbox"
      :b24ui="{
        root: 'ps-2 lg:ps-4',
        right: 'gap-3',
        title: 'text-[length:25px] font-(--ui-font-weight-medium) text-(--ui-color-base-1)'
      }"
    >
      <template #trailing>
        <B24Badge :label="filteredMails.length" color="air-secondary" />
      </template>

      <template #right>
        <B24Tabs
          v-model="selectedTab"
          :items="tabItems"
          :content="false"
          size="xs"
        />
      </template>
    </B24DashboardNavbar>

    <InboxList v-model="selectedMail" :mails="filteredMails" />
  </B24DashboardPanel>

  <InboxMail v-if="selectedMail" :mail="selectedMail" @close="selectedMail = null" />
  <div v-else class="hidden bg-(--ui-color-bg-content-primary) lg:flex flex-1 items-center justify-center">
    <MailOpenIcon class="size-32 text-dimmed" />
  </div>

  <B24Drawer v-if="isMobile" v-model:open="isMailPanelOpen">
    <template #content>
      <InboxMail v-if="selectedMail" :mail="selectedMail" @close="selectedMail = null" />
    </template>
  </B24Drawer>
</template>
