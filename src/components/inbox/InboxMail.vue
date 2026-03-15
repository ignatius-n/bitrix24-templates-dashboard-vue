<script setup lang="ts">
import type { Mail } from '../../types'
import type { DropdownMenuItem } from '@bitrix24/b24ui-nuxt'
import { ref } from 'vue'
import { format } from 'date-fns'
import { sleepAction } from '../../utils'
import CircleCheckIcon from '@bitrix24/b24icons-vue/outline/CircleCheckIcon'
import CrossLIcon from '@bitrix24/b24icons-vue/outline/CrossLIcon'
import MoveToIcon from '@bitrix24/b24icons-vue/outline/MoveToIcon'
import ReplyIcon from '@bitrix24/b24icons-vue/outline/ReplyIcon'
import MoreVerticalLIcon from '@bitrix24/b24icons-vue/outline/MoreVerticalLIcon'
import AttachIcon from '@bitrix24/b24icons-vue/outline/AttachIcon'
import SendIcon from '@bitrix24/b24icons-vue/outline/SendIcon'
import AlertAccentIcon from '@bitrix24/b24icons-vue/outline/AlertAccentIcon'
import CloudPauseIcon from '@bitrix24/b24icons-vue/main/CloudPauseIcon'
import LikeIcon from '@bitrix24/b24icons-vue/outline/LikeIcon'
import ChatsIcon from '@bitrix24/b24icons-vue/outline/ChatsIcon'

defineProps<{
  mail: Mail
}>()

const emits = defineEmits(['close'])

const dropdownItems = [
  { label: 'Mark as unread', icon: ChatsIcon },
  { label: 'Mark as important', icon: AlertAccentIcon },
  { type: 'separator' },
  { label: 'Star thread', icon: LikeIcon },
  { label: 'Mute thread', icon: CloudPauseIcon }
] as DropdownMenuItem[]

const toast = useToast()

const reply = ref('')
const loading = ref(false)

async function onSubmit() {
  loading.value = true

  await sleepAction(5000)
  toast.add({
    title: 'Email sent',
    description: 'Your email has been sent successfully',
    icon: CircleCheckIcon,
    color: 'air-primary-success'
  })

  reply.value = ''
  loading.value = false
}
</script>

<template>
  <B24DashboardPanel id="inbox-2">
    <B24DashboardNavbar :title="mail.subject" :toggle="false">
      <template #leading>
        <B24Button
          :icon="CrossLIcon"
          color="air-tertiary-no-accent"
          class="-ms-1.5"
          @click="emits('close')"
        />
      </template>

      <template #right>
        <B24Tooltip text="Archive">
          <B24Button
            :icon="MoveToIcon"
            color="air-tertiary-no-accent"
          />
        </B24Tooltip>

        <B24Tooltip text="Reply">
          <B24Button
            :icon="ReplyIcon"
            color="air-tertiary-no-accent"
          />
        </B24Tooltip>

        <!-- @todo: after UI update fix :b24ui -->
        <B24DropdownMenu
          :items="dropdownItems"
          :content="{ align: 'end', side: 'bottom', sideOffset: -2 }"
          :b24ui="{
            itemLabel: 'text-(--ui-color-base-1)',
            itemLeadingIcon: 'w-[25px] h-[25px] text-(--ui-color-base-5)',
            itemTrailingIcon: 'w-[25px] h-[25px]',
            itemLabelExternalIcon: 'w-[25px] h-[25px] text-(--ui-color-base-5)'
          }"
        >
          <B24Button
            :icon="MoreVerticalLIcon"
            color="air-tertiary-no-accent"
          />
        </B24DropdownMenu>
      </template>
    </B24DashboardNavbar>

    <div class="flex flex-col sm:flex-row justify-between gap-1 p-4 sm:px-6 border-b border-(--ui-color-divider-default)">
      <div class="flex items-start gap-4 sm:my-1.5">
        <B24Avatar
          v-bind="mail.from.avatar"
          :alt="mail.from.name"
          size="xl"
        />

        <div class="min-w-0">
          <p class="font-semibold text-highlighted">
            {{ mail.from.name }}
          </p>
          <p class="text-muted">
            {{ mail.from.email }}
          </p>
        </div>
      </div>

      <p class="max-sm:pl-16 text-muted text-sm sm:mt-2">
        {{ format(new Date(mail.date), 'dd MMM HH:mm') }}
      </p>
    </div>

    <div class="flex-1 p-4 sm:p-6 max-h-[200px] sm:max-h-max overflow-y-auto scrollbar-thin">
      <p class="whitespace-pre-wrap">
        {{ mail.body }}
      </p>
    </div>

    <div class="pb-4 px-4 sm:px-6 shrink-0">
      <!-- @todo: after UI update fix :b24ui -->
      <B24Card
        variant="tinted-no-accent"
        class="mt-auto"
        :b24ui="{
          root: 'bg-(--ui-color-bg-content-primary) light:bg-(--ui-color-gray-02)',
          header: 'flex items-center gap-1.5'
        }"
      >
        <template #header>
          <ReplyIcon class="size-5" />

          <span class="text-sm truncate">
            Reply to {{ mail.from.name }} ({{ mail.from.email }})
          </span>
        </template>

        <form @submit.prevent="onSubmit">
          <B24Textarea
            v-model="reply"
            required
            autoresize
            placeholder="Write your reply..."
            no-padding
            no-border
            :rows="2"
            :maxrows="5"
            :disabled="loading"
            class="w-full"
            :b24ui="{ base: 'resize-none' }"
          />

          <div class="flex items-center justify-between">
            <B24Tooltip text="Attach file">
              <B24Button
                color="air-tertiary-no-accent"
                :disabled="loading"
                :icon="AttachIcon"
              />
            </B24Tooltip>

            <div class="flex items-center justify-end gap-2">
              <B24Button
                color="air-tertiary-no-accent"
                :disabled="loading"
                label="Save draft"
              />
              <B24Button
                type="submit"
                color="air-primary"
                :loading="loading"
                label="Send"
                :icon="SendIcon"
              />
            </div>
          </div>
        </form>
      </B24Card>
    </div>
  </B24DashboardPanel>
</template>
