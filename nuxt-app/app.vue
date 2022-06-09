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
  start: 1999,
  end: 2025,
}
const periodMonth: intPerido = {
  start: 1,
  end: 12,
}

</script>

<template>
  <main>
    <siteHeader />
    <article class="container p-4 mx-auto flex flex-wrap">
      <div class="mx-auto">
        <div class="mb-12">
          <viewCalendar class="mx-auto" :set-day="setDate" />
        </div>
        <div>
          <label for="pet-select" class="block text-center mb-2">{{ year }}年{{ month }}月</label>
          <div class="bg-white shadow-md rounded flex justify-center">
            <select name="year" class="cursor-pointer text-center m-2" v-model="year">
              <option v-for="(year, i) in periodYear" :key="i">{{ year }}年</option>
            </select>
            <span class="self-center px-4">|</span>
            <select name="month" class="cursor-pointer text-center m-2" v-model="month">
              <option v-for="(month, i) in periodMonth" :key="i">{{ month }}月</option>
            </select>
          </div>
        </div>
      </div>
    </article>
  </main>
</template>
