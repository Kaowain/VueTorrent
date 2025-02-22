<script setup lang="ts">
import { useDialog } from '@/composables'
import { useAppStore, useAuthStore, useVueTorrentStore } from '@/stores'
import { useI18n } from 'vue-i18n'
import { toast } from 'vue3-toastify'

const props = defineProps<{
  guid: string
}>()

const { t } = useI18n()
const { isOpened } = useDialog(props.guid)
const appStore = useAppStore()
const authStore = useAuthStore()
const vueTorrentStore = useVueTorrentStore()

const close = () => {
  isOpened.value = false
}
const shutdown = async () => {
  if (await appStore.shutdownQbit()) {
    authStore.setAuthStatus(false)
    await vueTorrentStore.redirectToLogin()
    toast.success(t('dialogs.shutdown.success'))
  } else {
    toast.error(t('dialogs.shutdown.error'))
  }

  close()
}
</script>

<template>
  <v-dialog v-model="isOpened" width="auto">
    <v-card :title="$t('dialogs.shutdown.title')" :text="$t('dialogs.shutdown.content')">
      <v-card-actions class="justify-end">
        <v-spacer />
        <v-btn class="accent white--text elevation-0 px-4" variant="elevated" color="error" @click="shutdown">
          {{ $t('common.yes') }}
        </v-btn>
        <v-btn class="error white--text elevation-0 px-4" @click="close">
          {{ $t('common.no') }}
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<style scoped></style>
