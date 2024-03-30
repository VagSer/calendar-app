<script setup>
import { ref, computed } from 'vue';
import AppCalendar from './components/AppCalendar.vue';

const selectedLanguage = ref('ru')
const selectedFirstDay = ref('Sunday')

const isFirstDaySunday = computed(() => selectedFirstDay.value === 'Sunday')

const appLanguage = ref({
  ru: {
    daysShort: ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб'],
    firstDays: ['Воскресенье', 'Понедельник'],
    settingsTitle: 'Настройки',
    closeTitle: 'Закрыть',
    firstDayInAWeek: 'Первый день недели:',
    months: [
      'Январь', 'Февраль', 'Март', 
      'Апрель', 'Май', 'Июнь', 
      'Июль', 'Август', 'Сентябрь', 
      'Октябрь', 'Ноябрь', 'Декабрь'
    ]
  },
  en: {
    daysShort: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
    firstDays: ['Sunday', 'Monday'],
    settingsTitle: 'Settings',
    closeTitle: 'Close',
    firstDayInAWeek: 'First day in a week:',
    months: [
      'January', 'February', 'March', 
      'April', 'May', 'June', 
      'July', 'August', 'September', 
      'October', 'November', 'December'
    ]
  }
})

const openSettings = () => document.querySelector('#settings').showModal()
const closeSettings = () => document.querySelector('#settings').close()
</script>

<template>
  <h1>Моё приложение календаря</h1>
  <button @click="openSettings">
    {{ appLanguage[selectedLanguage].settingsTitle }}
  </button>
  <dialog id="settings">
    <form>
      <select v-model="selectedLanguage">
        <option value="ru">Русский</option>
        <option value="en">English</option>
      </select>
      <div>
        <label
          for="firstDay"
          >
          {{ appLanguage[selectedLanguage].firstDayInAWeek }}
        </label>
        <select v-model="selectedFirstDay" id="firstDay">
          <option value="Monday">{{ appLanguage[selectedLanguage].firstDays[1]}}</option>
          <option value="Sunday">{{ appLanguage[selectedLanguage].firstDays[0]}}</option>
        </select>
      </div>
      <button type="button" @click="closeSettings">
        {{ appLanguage[selectedLanguage].closeTitle }}
      </button>
    </form>
  </dialog>
  <AppCalendar 
    :isFirstDaySunday
    :lang="appLanguage[selectedLanguage]"
  />
  <hr />
  <h2>Если есть конкретная дата</h2>
  <AppCalendar
    date="1997-04-09"
    :isFirstDaySunday
    :lang="appLanguage[selectedLanguage]"
  />
</template>

<style scoped>
#settings {
  & form {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    height: 30svh;
  }
}
</style>
