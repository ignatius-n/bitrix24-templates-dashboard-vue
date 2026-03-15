<script setup lang="ts">
import { computed, ref } from 'vue'
import { useFetch } from '@vueuse/core'
import type { Member } from '../../types'
import SearchIcon from '@bitrix24/b24icons-vue/outline/SearchIcon'

const { data: members } = useFetch<Member[]>('https://dashboard-template.nuxt.dev/api/members', { initialData: [] }).json<Member[]>()

const q = ref('')

const filteredMembers = computed(() => {
  return members.value?.filter((member) => {
    return member.name.search(new RegExp(q.value, 'i')) !== -1 || member.username.search(new RegExp(q.value, 'i')) !== -1
  }) ?? []
})
</script>

<template>
  <div>
    <B24PageCard
      title="Members"
      description="Invite new members by email address."
      variant="tinted-alt"
      orientation="horizontal"
      class="mb-0"
      :b24ui="{
        root: 'rounded-none rounded-t-3xl',
        container: 'py-0 sm:py-0 lg:items-center lg:grid-cols-[1fr_auto]',
        title: 'text-(--ui-color-palette-gray-70)',
        description: 'text-(--ui-color-palette-gray-70)'
      }"
    >
      <!-- @todo: fix img -->
      <img
        src="https://b24-50g4ho.bitrix24.ru/bitrix/components/bitrix/intranet.invitation/templates/.default/images/department-control-icon.png"
        alt="Online workspace for the whole team"
        class="flex-1 size-[100px]"
      >
    </B24PageCard>
    <B24PageCard
      variant="outline-no-accent"
      :b24ui="{
        root: 'rounded-none rounded-b-3xl',
        container: 'p-0 sm:p-0 gap-y-0',
        wrapper: 'items-stretch',
        header: 'p-4 mb-0 border-b border-(--ui-color-divider-accent) dark:border-(--ui-color-divider-default)'
      }"
    >
      <template #header>
        <div class="flex flex-row flex-nowrap gap-2">
          <B24Input
            v-model="q"
            :icon="SearchIcon"
            placeholder="Search members"
            autofocus
            class="w-full"
          />
          <B24Button
            label="Invite"
            color="air-primary"
            class="w-fit lg:ms-auto"
          />
        </div>
      </template>

      <SettingsMembersList :members="filteredMembers" />
    </B24PageCard>
  </div>
</template>
