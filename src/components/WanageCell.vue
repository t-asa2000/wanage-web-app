<script setup lang="ts">
import { defineProps } from 'vue'
defineProps<{
  number: number
  count: number
}>()
</script>

<template>
  <div class="wanage-cell bg-light d-flex align-items-center justify-content-center">
    <div class="d-inline-block">
      <div class="h1 m-0 text-center">{{number}}</div>
      <span class="badge rounded-pill" :class="count > 0 ? 'bg-primary' : 'text-secondary'">{{count}}</span>
    </div>
  </div>
</template>

<style scoped>
.wanage-cell {
  font-size: 1.3em;
  min-width: 90px;
  min-height: 90px;
  max-width: 90px;
  max-height: 90px;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
}

.wanage-cell:hover {
  background-color: #eee !important;
}

.wanage-cell > div > span {
  font-family: monospace;
}
</style>