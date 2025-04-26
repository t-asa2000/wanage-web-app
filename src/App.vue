<script setup lang="ts">
import { ref, computed } from 'vue'
import WanageCell from './components/WanageCell.vue'

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

const storageNumbers = JSON.parse(localStorage.getItem('numbers') ?? '[]')
const numbers :number[] = storageNumbers.length == 9 ? storageNumbers : NUMBERS

const wanageMode = ref('add')
const wanageBoard = ref(numbers.map(number => {return {number, count: 0}}))

const wanageError = computed(() => {
  const numbers = wanageBoard.value.map(cell => cell.number)

  const duplicated = [...numbers].sort((a, b) => a - b)
    .some((number, index, arr) => index > 0 && number == arr[index - 1])
  if (duplicated) {
    //console.log('duplicated')
    return true
  }

  const sumNot15 = LINES.some(line => {
    const _numbers = line.map(index => numbers[index])
    const sum = _numbers.reduce((sum, number) => sum + number, 0)
    //console.log([..._numbers, sum])
    return sum != 15
  })

  if (sumNot15) {
    //console.log('sumNot15')
    return true
  }

  return false
})

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

function update(index: number) {
  switch (wanageMode.value) {
    case 'add':
      if (!wanageError.value && wanageCounts.value < 9) {
        wanageBoard.value[index].count++
      }
      break
    case 'remove':
      if (!wanageError.value && wanageBoard.value[index].count > 0) {
        wanageBoard.value[index].count--
      }
      break
    case 'edit':
      const {number} = wanageBoard.value[index]
      wanageBoard.value[index].number = (number % 9) + 1
      const numbers = wanageBoard.value.map(cell => cell.number)
      localStorage.setItem('numbers', JSON.stringify(numbers))
      break
  }
}

function resetCounts() {
  for (let index = 0; index < 9; index++) {
    wanageBoard.value[index].count = 0
  }
}

function resetBoard() {
  for (let index = 0; index < 9; index++) {
    wanageBoard.value[index].number = NUMBERS[index]
  }
  localStorage.setItem('numbers', JSON.stringify(NUMBERS))
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
    <div class="d-flex flex-wrap mt-3 gap-2">
      <input type="radio" class="btn-check" name="wanageMode" id="add" value="add" v-model="wanageMode" autocomplete="off">
      <label class="btn btn-outline-primary fw-bold" for="add">輪を数える</label>
      <input type="radio" class="btn-check" name="wanageMode" id="remove" value="remove" v-model="wanageMode" autocomplete="off">
      <label class="btn btn-outline-danger" for="remove">輪を減らす</label>
      <input type="radio" class="btn-check" name="wanageMode" id="edit" value="edit" v-model="wanageMode" autocomplete="off">
      <label class="btn btn-outline-success" for="edit">ボード編集</label>
      <button class="btn btn-secondary" @click.stop="resetCounts()">輪のリセット</button>
      <a href="." target="_blank" class="btn btn-secondary" v-if="wanageMode != 'edit'">新しいカード</a>
      <button class="btn btn-secondary" @click.stop="resetBoard()" v-else>ボードのリセット</button>
    </div>
    <template v-if="wanageMode == 'edit' || wanageError">
      <div class="alert mt-3" :class="wanageError ? 'alert-danger' : 'alert-success'">
        <b>現在の状態: {{ wanageError ? 'NG' : 'OK' }}</b><br>縦・横・斜めの合計が 15 になるようにし、数字の重複がないようにしてください。
      </div>
      <p>ボードの数字はブラウザのストレージに保存されます。</p>
    </template>
    <div class="d-grid mt-3 gap-2">
      <div class="d-flex gap-2">
        <WanageCell v-bind="wanageBoard[0]" @click.stop="update(0)" />
        <WanageCell v-bind="wanageBoard[1]" @click.stop="update(1)" />
        <WanageCell v-bind="wanageBoard[2]" @click.stop="update(2)" />
      </div>
      <div class="d-flex gap-2">
        <WanageCell v-bind="wanageBoard[3]" @click.stop="update(3)" />
        <WanageCell v-bind="wanageBoard[4]" @click.stop="update(4)" />
        <WanageCell v-bind="wanageBoard[5]" @click.stop="update(5)" />
      </div>
      <div class="d-flex gap-2">
        <WanageCell v-bind="wanageBoard[6]" @click.stop="update(6)" />
        <WanageCell v-bind="wanageBoard[7]" @click.stop="update(7)" />
        <WanageCell v-bind="wanageBoard[8]" @click.stop="update(8)" />
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
</style>
