<script setup lang="ts">
import dayjs from 'dayjs'
import { weekdaysShort as weekdays } from 'dayjs/locale/ja'
import ja from 'dayjs/locale/ja'
dayjs.locale(ja)
//import updateLocale from 'dayjs/plugin/updateLocale'
// dayjs.extend(updateLocale)
// dayjs.updateLocale('ja', {
//   weekdaysShort: ["月", "火", "水", "木", "金", "土", "日"]
// })

interface Props {
  setDay: string
}
const props = withDefaults(defineProps<Props>(), {
  setDay: '',
})

interface NowDate {
  nowDayWeek: Number
  today: String
  startWeekday: Number
  lastEndDate: Number
  endWeekday: Number
  endDate: Number
}

/**
 * 本日の日付 または Propsで渡された日付を返す
 */
const setDay = () => {
  if (props.setDay) {
    return dayjs(props.setDay)
  } else {
    return dayjs()
  }
}

const now = setDay() // 現在の日付情報を取得
const nowDate: NowDate = {
  nowDayWeek: now.day(), // 曜日 0(日曜日)~6(土曜日)
  today: now.format('YYYY年M月'), // 本日の日付（フォーマット付き）

  startWeekday: now.startOf('month').get('day'), // 月初の曜日
  lastEndDate: now.startOf('month').add(-1, 'day').get('date'), // 先月末日

  endWeekday: now.endOf('month').get('day'), // 月末の曜日
  endDate: now.endOf('month').get('date'), // 今月末日
}

//先月の日付で埋める
const lastMonth = []
for (let date: Number = nowDate.startWeekday; date > 0; date--) {
  lastMonth.push(Number(nowDate.lastEndDate) - Number(date) + 1)
}

//当月の日にちを出力
const thisMonth = {
  week1: [],
  week2: [],
}
//TODO: 日にちのデータ構造を見直す（週単位でオブジェクトにまとめる）
for (let date: Number = 1; date <= Number(nowDate.endDate); date++) {
  const current = now.set('date', Number(date))
  const weekday = current.get('day')
  if (date <= weekday) {
    thisMonth.week1.push(date)
  } else {
    thisMonth.week2.push(date)
  }
}

//次月の日付で埋める
const nextDay = []
for (let date: Number = 1; date < 7 - Number(nowDate.endWeekday); date++) {
  thisMonth.week2.push(date)
  nextDay.push(date)
}
function nextDays(d: Number) {
  const nextDaysClass = 7 - nowDate.endWeekday
  if (1 < d && d > nextDaysClass) {
    return false
  } else {
    return true
  }
}
</script>

<template>
  <table class="m-2">
    <caption>
      {{
        nowDate.today
      }}
    </caption>
    <tbody>
      <tr>
        <th v-for="(weekday, i) in weekdays" :key="i">{{ weekday }}</th>
      </tr>
      <tr>
        <td v-for="(day, i) in lastMonth" :key="i" class="text-slate-400 pointer-events-none">{{ day }}</td>
        <td v-for="(day, i) in thisMonth.week1" :key="i">{{ day }}</td>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="i <= 6">{{ day }}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="6 < i && i <= 13">{{ day }}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="13 < i && i <= 20">{{ day }}</td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="20 < i && i <= 27" v-bind:class="{ 'text-slate-400 pointer-events-none': nextDays(day) }">
            {{ day }}
          </td>
        </template>
      </tr>
      <tr>
        <template v-for="(day, i) in thisMonth.week2" :key="i">
          <td v-if="27 < i" v-bind:class="{ 'text-slate-400 pointer-events-none': nextDays(day) }">{{ day }}</td>
        </template>
      </tr>
    </tbody>
  </table>
</template>

<style>
td,
th {
  text-align: center;
  padding: 8px;
  border: 1px gray solid;
}
</style>
