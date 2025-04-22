<script setup lang="ts">
import { ref, computed } from 'vue'

const NUMBERS = [2,9,4,7,5,3,6,1,8]
const LINES = [
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 4, 8],
  [2, 4, 6]
]

const mode = ref('add')
const wanageBoard = ref(NUMBERS.map(number => {return {number, count: 0}}))
const wanageCounts = computed(() => {
  return wanageBoard.value.reduce((counts, cell) => counts + cell.count, 0)
})
const wanagePoints = computed(() => {
  const board = wanageBoard.value.map(cell => {return {...cell, lines: 0}})
  
  const perfect = board.every(cell => cell.count > 0)
  if (perfect) return 300
  
  const linePoints = LINES.reduce((points, line) => {
    const counts = line.map(index => board[index].count)
    const min = counts.sort((a, b) => a - b)[0]
    line.forEach(index => board[index].lines += min)
    return points + min * 30
  }, 0)

  const otherPoints = board.reduce((points, cell) => {
    const count = cell.count - cell.lines
    return points + (count > 0 ? count * cell.number : 0)
  }, 0)

  return linePoints + otherPoints
})

function getDate() {
  return new Date().toLocaleDateString()
}

function updateCount(index: number) {
  switch (mode.value) {
    case 'add':
      if (wanageCounts.value < 9) {
        wanageBoard.value[index].count++
      }
      break
    case 'remove':
      if (wanageBoard.value[index].count > 0) {
        wanageBoard.value[index].count--
      }
      break
  }
}

function reset() {
  for (let index = 0; index < 9; index++) {
    wanageBoard.value[index].count = 0
  }
}

</script>

<template>
  <nav class="navbar navbar-expand-md navbar-light bg-light mb-4">
    <div class="container-fluid">
      <a class="navbar-brand" href=".">輪投げ点数カード</a>
    </div>
  </nav>
  <main class="container-fluid">
    <div>
      <p class="h2">{{ getDate() }}</p>
      <input type="text" class="form-control fw-bold" id="name" placeholder="プレイヤー名">
    </div>
    <div class="d-flex mt-3 gap-2">
      <div class="btn-group" role="group" aria-label="Basic radio toggle button group">
        <input type="radio" class="btn-check" name="mode" id="add" value="add" v-model="mode" autocomplete="off">
        <label class="btn btn-outline-primary px-4" for="add">＋</label>
        <input type="radio" class="btn-check" name="mode" id="remove" value="remove" v-model="mode" autocomplete="off">
        <label class="btn btn-outline-danger px-4" for="remove">－</label>
      </div>
      <button class="btn btn-secondary px-3" @click.stop="reset()">リセット</button>
      <a href="." target="_blank" class="btn btn-secondary px-3">新しいカード</a>
    </div>
    
    <div class="d-grid mt-3 gap-2">
      <div class="d-flex gap-2">
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center" @click.stop="updateCount(0)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[0].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[0].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[0].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(1)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[1].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[1].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[1].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(2)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[2].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[2].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[2].count}}</span>
          </div>
        </div>
      </div>
      <div class="d-flex gap-2">
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(3)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[3].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[3].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[3].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(4)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[4].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[4].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[4].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(5)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[5].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[5].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[5].count}}</span>
          </div>
        </div>
      </div>
      <div class="d-flex gap-2">
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(6)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[6].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[6].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[6].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center"  @click.stop="updateCount(7)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[7].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[7].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[7].count}}</span>
          </div>
        </div>
        <div class="wanage-cell bg-light d-flex align-items-center justify-content-center" @click.stop="updateCount(8)">
          <div class="d-inline-block">
            <div class="h1 m-0 text-center">{{wanageBoard[8].number}}</div>
            <span class="badge rounded-pill" :class="wanageBoard[8].count > 0 ? 'bg-primary' : 'text-secondary'">{{wanageBoard[8].count}}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="mt-3">
      <h2>現在の得点: {{ wanagePoints }}</h2>
    </div>
  </main>
</template>

<style scoped>
#name {
  font-size: 1.3em;
  max-width: 180px;
}

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

.wanage-cell > div > span {
  font-family: monospace;
}
</style>
