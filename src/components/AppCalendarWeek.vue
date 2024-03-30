<script setup>
const emit = defineEmits(['update:selectedDate'])

const props = defineProps({
  week: {
    type: Array,
    required: true,
  },
  month: {
    type: Number,
    required: true,
  },
  selectedDate: {
    type: Object,
    required: true,
  }
})

const checkMonth = (day) => day.getMonth() === props.month

const checkSelected = (day) => {
  return day.getDate() === props.selectedDate.getDate()
  && day.getMonth() === props.selectedDate.getMonth()
  && day.getFullYear() === props.selectedDate.getFullYear()
} 

const updateDate = (day) => emit('update:selectedDate', day)
</script>

<template>
  <tr>
    <td 
      v-for="day of week" :key="day.getDate()"
      :class="checkMonth(day)? 'day' : 'day another'"
    >
      <button 
        @click="updateDate(day)"
        :class="checkSelected(day)? 'selected' : ''"
      >
        {{ day.getDate() }}
      </button>
    </td>
  </tr>
</template>

<style scoped>
.day {
  border: 1px solid;
  
  & button {
    border: none;
    background: none;
    cursor: pointer;
  }

  & .selected {
    background-color: oklch(80% 0.17 250);
    border-radius: 4px;
  }
}

.another {
  opacity: 0.4;
}
</style>