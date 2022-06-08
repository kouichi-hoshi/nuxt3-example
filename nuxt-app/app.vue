<script setup lang="ts">
import dayjs from 'dayjs'
import { ref } from "vue"

const year = ref(dayjs().format('YYYY'))
const month = ref(dayjs().format('M'))
const setDate = ref(computed(() =>  year.value + "-" + month.value))

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
          <label for="pet-select" class="block text-center mb-2">{{year}}年{{month}}月</label>
          <div class="bg-white shadow-md rounded flex justify-center">
            <select name="year" class="cursor-pointer text-center m-2" v-model="year">
              <template v-for="(year, i) in 2023" :key="i">
                <option v-if="2020 <= year" :value="year">
                  {{year}}年
                </option>
              </template>
            </select>
            <span class="self-center px-4">|</span>
            <select name="month" class="cursor-pointer text-center m-2" v-model="month">
                <template v-for="(month, i) in 12" :key="i">
                  <option v-if="1 <= month" :value="month">
                    {{month}}月
                  </option>
                </template>
            </select>
          </div>
        </div>
      </div>
    </article>
  </main>
</template>
