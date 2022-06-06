<script setup lang="ts">
import dayjs from 'dayjs'
import {weekdaysShort as weekdays} from 'dayjs/locale/ja'
import ja from 'dayjs/locale/ja';
dayjs.locale(ja);

interface NowDate {
  nowDayWeek: Number,
  lastEndDate: Number
  startWeekday: Number
  endDate: Number
  endWeekday: Number
}

const now = dayjs('2021-6-10') // 現在の日付情報を取得
const nowDate:NowDate = {
  nowDayWeek: now.day(), // 曜日 0(日曜日)~6(土曜日)
  lastEndDate: now.startOf('month').add(-1, 'day').get('date'), // 先月末日
  startWeekday: now.startOf('month').get('day'), // 月初の曜日
  endDate: now.endOf('month').get('date'), // 月末の日にち
  endWeekday: now.endOf('month').get('day'), // 月末の曜日
}

//先月の日付で埋める
const lastMonth = []
for (let date:Number = nowDate.startWeekday; date > 0; date--) {
  lastMonth.push(Number(nowDate.lastEndDate) - Number(date) + 1)
}

//当月の日にちを出力
const thisMonth = {
  week1: [],
  week2: [],
  week3: [],
}
const thisMonth2 = []
for (let date:Number = 1; date <= nowDate.endDate; date++) {
  const current = now.set('date', Number(date))
  const weekday = current.get('day')
  if (date <= weekday) {
    thisMonth.week1.push(date)
    thisMonth2[1] = date
  } else {
    thisMonth.week2.push(date)
    thisMonth2[2] = date
  }
}

</script>

<template>
  <header>
    <h2>Calendar</h2>
  </header>
  <section class="p-4">
    <table>
      <tr>
        <th v-for="(weekday, i) in weekdays" :key="i">{{weekday}}</th>
      </tr>
      <tr>
        <td v-for="(day, i) in lastMonth" :key="i" class="text-slate-400">{{day}}</td>
        <td v-for="(day, i) in thisMonth.week1" :key="i">{{day}}</td>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="i <= 6">{{day}}</td>
      </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="6 < i && i <= 13">{{day}}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="13 < i && i <= 20">{{day}}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="20 < i && i <= 27">{{day}}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="27 < i">{{day}}</td>
        </template>
      </tr>
    </table>
  </section>

<table>

</table>

</template>

<style>
table, td, th {
  text-align: center;
  padding: 8px;
  border: 1px gray solid;
}
</style>