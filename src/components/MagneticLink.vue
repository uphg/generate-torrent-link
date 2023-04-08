

<template>
  <h2>生成磁链</h2>
  <div class="magnetic-link">
    <input class="input" type="text" v-model="input">
    <button class="button" @click="copyLink">Copy</button>
  </div>
  <div class="output">
    Current copied: <code class="code">{{ magneticLink || 'none' }}</code>
  </div>
  <h2 style="padding-top: 1.2rem; padding-bottom: 0.4rem;">历史记录</h2>
  <div class="history">
    <div class="history-item" v-for="item, index in history" :key="item.date + index">
      <span class="date">{{ dateFormat(item.date) }}</span><code class="code">{{ item.text }}</code><span class="delete" @click="removeHistory(item)">删除</span>
    </div>
    <div>...</div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { useClipboard, useDateFormat, useStorage, type DateLike } from '@vueuse/core'

type HistoryItem = { id: string, text: string, date: number }

const input = ref('')
const history = useStorage<HistoryItem[]>('my-history', [], localStorage)
const { copy } = useClipboard()

const magneticLink = computed(() => input.value && `magnet:?xt=urn:btih:${input.value}&tr=http://open.acgtracker.com:1096/announce`)

function copyLink() {
  if (!input.value) return
  copy(magneticLink.value).then(() => {
    const item = {
      id: `${Date.now()}${Math.floor(Math.random() * 6 + 1)}`,
      text: magneticLink.value,
      date: Date.now()
    }
    const length = history.value.length
    if (length >= 200) {
      history.value.pop()
    }
    history.value.unshift(item)
  })
}

function removeHistory(current: HistoryItem) {
  const index = history.value.findIndex(history => history.id === current.id)
  history.value.splice(index, 1)
}

function dateFormat(value: DateLike) {
  return useDateFormat(value, 'YYYY-MM-DD HH:mm:ss').value
}
</script>

<style scoped>
.magnetic-link {
  display: flex;
  gap: 12px;
}

.output {
  margin-top: 0.4rem;
}
.code {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 920px;
  color: #000;
  font-family: Consolas;
}
.history-item {
  display: flex;
}
.date {
  color: #48484f;
  padding-right: 0.6rem;
}

.delete {
  font-size: 14px;
  cursor: pointer;
  margin: 0 0.4rem;
  margin-left: auto;
}
.delete:hover {
  border-bottom: 1px solid #000;
}
</style>
