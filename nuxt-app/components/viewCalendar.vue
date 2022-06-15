<script setup lang="ts">
import { ref, reactive, watch, watchEffect } from 'vue'
import dayjs from 'dayjs'
import { weekdaysShort as weekdays } from 'dayjs/locale/ja'
import ja from 'dayjs/locale/ja'
dayjs.locale(ja)
//import updateLocale from 'dayjs/plugin/updateLocale'
// dayjs.extend(updateLocale)
// dayjs.updateLocale('ja', {
//   weekdaysShort: ["月", "火", "水", "木", "金", "土", "日"]
// })

/**
 * カレンダー初期設定
 */

interface Props {
  setDay: string
}

// カレンダーの起点年月をPropsで受取
const props = withDefaults(defineProps<Props>(), {
  setDay: '',
})

// Propsで渡された年月または本日の年月を返す
function setDay() {
  if (props.setDay) {
    return dayjs(props.setDay)
  } else {
    return dayjs()
  }
}

// カレンダー起点年月を生成
let now = setDay()

interface NowDate {
  nowDayWeek: Number
  today: String
  startWeekday: Number
  lastEndDate: Number
  endWeekday: Number
  endDate: Number
  nowYM: String
}

// 基本情報オブジェクト
const nowDate: NowDate = reactive({
  nowDayWeek: 0, // 曜日 0(日曜日)~6(土曜日)
  today: "", // 本日の日付（フォーマット付き）
  startWeekday: 0, // 月初の曜日
  lastEndDate: 0, // 先月末日
  endWeekday: 0, // 月末の曜日
  endDate: 0, // 今月末日
  nowYM: "",
})

// TODO: 最適な形に分割・共通化する
interface TheMonth {
  allDays: Number[],
  week1: Number[],
  week2: Number[],
  week3: Number[],
  week4: Number[],
  week5: Number[],
  week6: Number[],
  prevStartWeekDay: Number[]
}

// カレンダー日にちオブジェクト
// TODO: 最適な形に分割・統合する
const theMonth:TheMonth = {
  allDays: [],
  week1: [],
  week2: [],
  week3: [],
  week4: [],
  week5: [],
  week6: [],
  prevStartWeekDay: [], // 先月の週末の補完個数
}


/**
 *更新処理
 */

// カレンダーの起点年月を変更
//TODO propsで年月データが渡されなかった場合、更新されない不具合あり
function updateDay() {
  if (props.setDay) {
    now = dayjs(props.setDay)
  } else {
    // TODO 本日の日付を渡すのではなく、select要素で指定した年月を渡す
    now = dayjs()
  }
  console.log(now)
}

//基準データをセットする
function setNowDate() {
  nowDate.nowDayWeek = now.day() // 曜日 0(日曜日)~6(土曜日)
  nowDate.today = now.format('YYYY年M月') // 今月の見出し
  nowDate.startWeekday = now.startOf('month').get('day') // 月初の曜日
  nowDate.lastEndDate = now.startOf('month').add(-1, 'day').get('date') // 先月末日
  nowDate.endWeekday = now.endOf('month').get('day') // 月末の曜日
  nowDate.endDate = now.endOf('month').get('date') // 今月末日
  nowDate.nowYM = dayjs().format('YYYY年M月')
}

// 表示中のカレンダーの日付の中で今日の日付を検知したらtrueを返す
function checkToday() {
  if (nowDate.today === nowDate.nowYM) {
    return Number(dayjs().format('DD'))
  }
}

// カレンダーリセット処理
function initTeMonth() {

  // セットされている日にちをクリア
  theMonth.allDays.splice(0, theMonth.allDays.length)
  theMonth.prevStartWeekDay.splice(0, theMonth.prevStartWeekDay.length)

  // リセット対象配列をセット
  // TODO: 配列を動的生成に変更
  const week = [
    theMonth.week1, theMonth.week2,
    theMonth.week3, theMonth.week4,
    theMonth.week5, theMonth.week6,
  ]

  // セットされているカレンダーをクリア
  week.forEach((v, i) => {
    week[i].splice(0,week[i].length)
  })

}

// カレンダーの日にちをセットする
function setMonthDays() {

  // 複数データを直列処理するためのindex値
  let count :any = Number(0)

  // 今月の一週間目の空欄（1日以前の日にち）を先月の日にちでセットする
  for (let i:any = nowDate.startWeekday; i > 0; i--) {
    theMonth.allDays.push(Number(nowDate.lastEndDate) - Number(i) + 1)
    count++
  }

  // セットする先月の週末の日にちの個数をセットする
  theMonth.prevStartWeekDay.push(count)

  // 今月のすべての日にちをセットする
  for (let i = 1; i <= nowDate.endDate; i ++) {
    theMonth.allDays.push(i)
  }

  // 今月の最終週の空欄（末日以後の日にち）を来月の日にちでセットする
  let last = []
  for (let i = 1; i < (7 - Number(nowDate.endWeekday)); i++) {
    theMonth.allDays.push(i)
  }

}

/**
 * 先月 または 来月の日にちであればtrueを返す（要素にclassを付ける）
 * @param month check対象の月をセットする  "prev"←先月 "next"←来月
 * @param day check対象の日にちを受け取る
 */
function checkDate(month:String, day:Number) {
  const prev = Number(theMonth.prevStartWeekDay)

  //先月の日付であるかチェックする
  if (month === "prev") {
    if (prev <= day) {
      return false
    } else {
      return true
    }
  }

  //来月の日付であるかチェックする
  if (month === "next") {
    if (day > 1 && 7 < day) {
      return false
    } else {
      return true
    }
  }
}

// 今月のカレンダーの日にちを週単位で分割する
function splitWeek() {
  theMonth.allDays.forEach( (v, i)=> {
    if(i < 7) {
      theMonth.week1.push(v)
    } else if(i < 14) {
      theMonth.week2.push(v)
    } else if(i < 21) {
      theMonth.week3.push(v)
    } else if(i < 28) {
      theMonth.week4.push(v)
    } else if(i < 35) {
      theMonth.week5.push(v)
    } else if(i < 42) {
      theMonth.week6.push(v)
    }
  })
}

/**
 * 初期化処理実行 / 更新監視
 */
watchEffect(()=> {
  // TODO: watchEffectの機能を再確認
  // computed get関数 set関数 への書き換えも検討する
  props
  updateDay()
  setNowDate()
  initTeMonth()
  setMonthDays()
  splitWeek()
})

</script>

<template>
  <table>
    <caption class="mb-6 text-lg">
      {{
        nowDate.today
      }}
    </caption>
    <thead>
      <tr>
        <th v-for="(weekday, i) in weekdays" :key="i">{{ weekday }}</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td v-for="(day, i) in theMonth.week1" :key="i" :class="[{'today' : day === checkToday()}, { 'prev text-slate-400 pointer-events-none': checkDate('prev', i) }]">{{ day }}</td>
      </tr>
      <tr>
        <td v-for="(day, i) in theMonth.week2" :key="i" :class="{'today' : day === checkToday()}">{{ day }}</td>
      </tr>
      <tr>
        <td v-for="(day, i) in theMonth.week3" :key="i" :class="{'today' : day === checkToday()}">{{ day }}</td>
      </tr>
      <tr>
        <td v-for="(day, i) in theMonth.week4" :key="i" :class="{'today' : day === checkToday()}">{{ day }}</td>
      </tr>
      <tr>
        <td v-for="(day, i) in theMonth.week5" :key="i" v-bind:class="[{'today' : day === checkToday()}, { 'next text-slate-400 pointer-events-none': checkDate('next',day) }]">{{ day }}</td>
      </tr>
      <tr v-if="theMonth.week6.length > 1">
        <td v-for="(day, i) in theMonth.week6" :key="i" v-bind:class="[{'today' : day === checkToday()},{ 'next text-slate-400 pointer-events-none': checkDate('next', day) }]">{{ day }}</td>
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
.today {
  color: #ce3131;
  font-weight: bold;
}
</style>
