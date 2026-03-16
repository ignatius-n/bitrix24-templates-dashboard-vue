<script setup lang="ts">
import type { NavigationMenuItem } from '@bitrix24/b24ui-nuxt'
import InfoCircleIcon from '@bitrix24/b24icons-vue/outline/InfoCircleIcon'
import ShieldIcon from '@bitrix24/b24icons-vue/outline/ShieldIcon'
import Bell1Icon from '@bitrix24/b24icons-vue/main/Bell1Icon'
import PersonIcon from '@bitrix24/b24icons-vue/outline/PersonIcon'
import ThreePersonsIcon from '@bitrix24/b24icons-vue/outline/ThreePersonsIcon'

const links = [
  [
    {
      label: 'General',
      icon: PersonIcon,
      to: '/settings',
      exact: true
    },
    {
      label: 'Members',
      icon: ThreePersonsIcon,
      to: '/settings/members'
    },
    {
      label: 'Notifications',
      icon: Bell1Icon,
      to: '/settings/notifications'
    },
    {
      label: 'Security',
      icon: ShieldIcon,
      to: '/settings/security'
    }
  ],
  [
    {
      label: 'Documentation',
      icon: InfoCircleIcon,
      to: 'https://bitrix24.github.io/b24ui/docs/getting-started/installation/vue/',
      target: '_blank'
    }
  ]
] satisfies NavigationMenuItem[][]
</script>

<template>
  <!-- @todo: after UI update fix :b24ui -->
  <B24DashboardPanel
    id="settings"
    :b24ui="{ body: 'sm:p-4 lg:py-12 scrollbar-transparent' }"
  >
    <template #header>
      <!-- @todo: after UI update fix :b24ui -->
      <B24DashboardNavbar
        title="Settings"
        :b24ui="{
          root: 'ps-2 lg:ps-4 border-0',
          right: 'gap-3',
          title: 'text-[length:25px] font-(--ui-font-weight-medium) text-(--ui-color-base-1)'
        }"
      >
        <template #right>
          <B24Button
            size="sm"
            label="Feedback"
          />
        </template>
      </B24DashboardNavbar>

      <!-- @todo: after UI update fix :b24ui -->
      <B24DashboardToolbar class="scrollbar-thin" :b24ui="{ root: 'sm:px-4 border-0' }">
        <B24NavigationMenu :items="links" highlight class="flex-1" />
      </B24DashboardToolbar>
    </template>

    <template #body>
      <div class="flex flex-col gap-4 sm:gap-6 lg:gap-12 w-full lg:max-w-[672px] mx-auto">
        <RouterView />
      </div>
    </template>
  </B24DashboardPanel>
</template>
