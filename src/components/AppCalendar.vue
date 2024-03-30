<script setup>
import { ref, computed, onMounted } from 'vue';
import AppCalendarWeek from './AppCalendarWeek.vue';
import AppCalendarDaysNames from './AppCalendarDaysNames.vue';

const props = defineProps({
  isFirstDaySunday: {
    type: Boolean,
    default: true,
  },
  lang: {
    type: Object,
    required: true
  },
  date: {
    type: String,
    default: ''
  }
})


const currentDate = ref({})

const selectedDate = ref({})

const monthName = computed(() => {
  return props.lang.months[currentDate.value.Month]
})

const lastDayInMonth = computed(() => {
  let number = 0
  switch (currentDate.value.Month) {
    case 1: 
      number = currentDate.value.Year % 4 === 0? 29 : 28;
      break;
    case 3:
    case 5:
    case 8:
    case 10: 
      number = 30; 
      break;
    default: 
      number = 31;
  }
  return number
})

const totalMonth = computed(() => {
  let month = [];
  let firstFullWeek = [];
  if (!currentDate.value.Day) return;
  const firstDay = new Date(currentDate.value.Year, currentDate.value.Month, 1);
  let innerCurrentDate = new Date(firstDay.getFullYear(), firstDay.getMonth(), 1);
  let dayOfTheWeek = 0
  let isWeekFull = false
  while (!isWeekFull) {
    if (innerCurrentDate.getDay() === dayOfTheWeek) {
        firstFullWeek.push(innerCurrentDate)
        innerCurrentDate = new Date(
          firstDay.getFullYear(), 
          firstDay.getMonth(), 
          innerCurrentDate.getDate() + 1);
      }
    if (!props.isFirstDaySunday) {
      if (dayOfTheWeek === 0 && firstFullWeek.length < 7) {
        firstFullWeek.length = 0
      }
      dayOfTheWeek++
      if (dayOfTheWeek > 6) {
        dayOfTheWeek = 0
      }
    } else {
        dayOfTheWeek++
        if (dayOfTheWeek === 7 && firstFullWeek.length !== 7) {
          firstFullWeek.length = 0;
          dayOfTheWeek = 0;
        }
      }
    isWeekFull = firstFullWeek.length === 7
  }
  month.push(firstFullWeek)
  if (firstFullWeek[0].getDate() > 1) {
    let firstWeekInMonth = [];
    firstFullWeek.forEach((day) => {
      firstWeekInMonth.push(new Date(day.getFullYear(), day.getMonth(), day.getDate()-7));
    });
    month.unshift(firstWeekInMonth);
  }
  let isLastDayWasAdded = false;
  while(!isLastDayWasAdded) {
    let newWeek = [];
    month.at(-1).forEach((day) => {
      newWeek.push(new Date(day.getFullYear(), day.getMonth(), day.getDate()+7))
    })
    month.push(newWeek)
    isLastDayWasAdded = newWeek.some((day) => day.getDate() === lastDayInMonth.value)
  }
  return month;
})

const updateMonth = (newValue) => {
  if (newValue > 0) {
    if (currentDate.value.Month === 11) {
      currentDate.value.Month = 0
      currentDate.value.Year++
    } else {
      currentDate.value.Month++
    }
  } else {
    if (currentDate.value.Month === 0) {
      currentDate.value.Month = 11
      currentDate.value.Year--
    } else {
      currentDate.value.Month--
    }
  }
}

onMounted(() => {
  let newDate = null
  if (props.date) {
    newDate = new Date(props.date)
  } else {
    newDate = new Date()
  }
  currentDate.value = {
    Day: newDate.getDate(),
    Month: newDate.getMonth(),
    Year: newDate.getFullYear(),
  }
  selectedDate.value = new Date(newDate.getFullYear(), newDate.getMonth(), newDate.getDate())
})
</script>

<template>
  <table class="calendar">
    <thead>
      <tr>
        <td>
          <button @click="updateMonth(-1)">
            ◀
          </button>
        </td>
        <td colspan="5">{{ monthName }}, {{ currentDate.Year }}</td>
        <td>
          <button @click="updateMonth(+1)">
            ▶
          </button>
        </td>
      </tr>
    </thead>
    <tbody>
      <AppCalendarDaysNames :isFirstDaySunday :daysList="props.lang.daysShort"/>
      <AppCalendarWeek
        v-for="week of totalMonth"
        :key="week[0]"
        :month="currentDate.Month"
        v-model:selectedDate="selectedDate"
        :week
      />
    </tbody>
  </table>
</template>

<style scoped>
.calendar {
  text-align: center;
  border: 1px solid;
  border-collapse: collapse;
  width: min(90%, 420px);
  margin: auto;
  & tbody {
    & td, th {
      border: 1px solid;
    }
  }

  & thead {
    background-color: oklch(80% 0.17 250);

    & button {
      background: none;
      border: none;
      cursor: pointer;
    }
  }
}
</style>