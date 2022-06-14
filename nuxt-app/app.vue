<script setup lang="ts">
import dayjs from 'dayjs'
import { ref, computed } from 'vue'

const year = ref(dayjs().format('YYYY'))
const month = ref(dayjs().format('M'))
const setDate = ref(computed(() => year.value + '-' + month.value))

interface intPerido {
  start: Number
  end: Number
}
const periodYear: intPerido = {
  start: Number(year.value) - 5,
  end: Number(year.value) + 5,
}
const periodMonth: intPerido = {
  start: 1,
  end: 13,
}

// 日付の選択肢データを生成
function createSequence(start:Number, end:Number) {
  const sequence = []
  for (let i:any = start; i < end; i++) {
    sequence.push(i)
  }
  return sequence
}

</script>

<template>
  <main>
    <siteHeader class="mb-6" />
    <article class="container mx-auto p-4">
      <viewCalendar class="mx-auto mb-12" :set-day="setDate" />
      <div class="flex justify-center mb-6">
        <div class="flex-initial bg-white shadow-md rounded mx-1">
          <select name="year" class="cursor-pointer text-center p-4 mr-2" v-model="year">
            <option
              v-for="(year, i) in createSequence(periodYear.start, periodYear.end)"
              :key="i"
            >
              {{ year }}
            </option>
          </select>
        </div>
        <div class="flex-initial bg-white shadow-md rounded mx-1">
          <select name="month" class="cursor-pointer text-center p-4 mr-2" v-model="month">
            <option
              v-for="(year, i) in createSequence(periodMonth.start, periodMonth.end)"
              :key="i"
            >
              {{ year }}
            </option>
          </select>
        </div>
      </div>
      <div class="flex justify-center">
        <div class="flex-initial bg-white shadow-md rounded mx-1"><button class="p-4 mx-2">すすむ</button></div>
        <div class="flex-initial bg-white shadow-md rounded mx-1"><button class="p-4 mx-2">もどる</button></div>
        <div class="flex-initial bg-white shadow-md rounded mx-1"><button class="p-4 mx-2">今月</button></div>
      </div>
    </article>
  </main>
</template>