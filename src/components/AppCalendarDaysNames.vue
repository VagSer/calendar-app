<script setup>
import { ref, onMounted, watchEffect } from 'vue';

const props = defineProps({
  isFirstDaySunday: {
    type: Boolean,
    default: true
  },
  daysList: {
    type: Array,
    required: true,
  }
})

const daysOfTheWeek = ref([]);

const updateWeek = () => {
  daysOfTheWeek.value = [...props.daysList]
  if (!props.isFirstDaySunday) {
    daysOfTheWeek.value.push(daysOfTheWeek.value[0])
    daysOfTheWeek.value.shift()
  }
}

watchEffect(() => {
  updateWeek()
})

onMounted(() => {
  daysOfTheWeek.value = [...props.daysList]
})
</script>

<template>
  <tr>
    <th v-for="name of daysOfTheWeek" :key="name">
      {{ name }}
    </th>
  </tr>
</template>

<style>
th {
  border: 1px solid;
}
</style>