<script >
import { ref, computed, onMounted } from 'vue'

export default {
  name: 'Calendar',
  setup() {
    const currentDate = ref(new Date())
    const selectedDay = ref(null)
    
    // Названия дней недели
    const weekDays = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс']
    
    // Текущий месяц и год
    const currentMonthYear = computed(() => {
      return currentDate.value.toLocaleDateString('ru-RU', { 
        month: 'numeric', 
        year: 'numeric' 
      })
    })
    
    // Форматированная дата выбранного дня
    const formattedSelectedDate = computed(() => {
      if (!selectedDay.value) return ''
      return selectedDay.value.toLocaleDateString('ru-RU', {
        day: 'numeric',
        month: 'long',
        year: 'numeric'
      })
    })
    
    // Получить первый день месяца
    const getFirstDayOfMonth = (date) => {
      return new Date(date.getFullYear(), date.getMonth(), 1)
    }
    
    // Получить последний день месяца
    const getLastDayOfMonth = (date) => {
      return new Date(date.getFullYear(), date.getMonth() + 1, 0)
    }
    
    // Получить дни для отображения в календаре
    const calendarDays = computed(() => {
      const year = currentDate.value.getFullYear()
      const month = currentDate.value.getMonth()
      
      const firstDay = getFirstDayOfMonth(currentDate.value)
      const lastDay = getLastDayOfMonth(currentDate.value)
      
      // Начинаем с понедельника (1), воскресенье = 0
      const firstDayOfWeek = firstDay.getDay() === 0 ? 6 : firstDay.getDay() - 1
      const lastDayOfWeek = lastDay.getDay() === 0 ? 6 : lastDay.getDay() - 1
      
      const days = []
      
      // Добавляем дни из предыдущего месяца
      const prevMonthLastDay = new Date(year, month, 0).getDate()
      for (let i = firstDayOfWeek - 1; i >= 0; i--) {
        const date = new Date(year, month - 1, prevMonthLastDay - i)
        days.push({
          date,
          dayNumber: date.getDate(),
          isCurrentMonth: false
        })
      }
      
      // Добавляем дни текущего месяца
      for (let i = 1; i <= lastDay.getDate(); i++) {
        const date = new Date(year, month, i)
        days.push({
          date,
          dayNumber: i,
          isCurrentMonth: true
        })
      }
      
      // Добавляем дни следующего месяца
      const daysNeeded = 42 - days.length // 6 недель * 7 дней
      for (let i = 1; i <= daysNeeded; i++) {
        const date = new Date(year, month + 1, i)
        days.push({
          date,
          dayNumber: i,
          isCurrentMonth: false
        })
      }
      
      return days
    })
    
    // Перейти к предыдущему месяцу
    const prevMonth = () => {
      currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() - 1,
        1
      )
    }
    
    // Перейти к следующему месяцу
    const nextMonth = () => {
      currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() + 1,
        1
      )
    }
    
    // Выбрать день
    const selectDay = (day) => {
      selectedDay.value = day.date
      console.log('Выбран день:', day.date.toLocaleDateString('ru-RU'))
    }
    
    // Проверить, является ли день выбранным
    const isSelected = (date) => {
      if (!selectedDay.value) return false
      return date.toDateString() === selectedDay.value.toDateString()
    }
    
    // Проверить, является ли день сегодняшним
    const isToday = (date) => {
      const today = new Date()
      return date.toDateString() === today.toDateString()
    }
    
    // Инициализация - выбрать текущий день
    onMounted(() => {
      selectedDay.value = new Date()
    })
    
    return {
      currentDate,
      selectedDay,
      weekDays,
      currentMonthYear,
      formattedSelectedDate,
      calendarDays,
      prevMonth,
      nextMonth,
      selectDay,
      isSelected,
      isToday
    }
  }
}
</script>

<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevMonth">&lt;</button>
      <h2>{{ currentMonthYear }}</h2>
      <button @click="nextMonth">&gt;</button>
    </div>
    
    <div class="calendar-grid">
      <div class="weekday" v-for="day in weekDays" :key="day">
        {{ day }}
      </div>
      
      <div 
        v-for="day in calendarDays" 
        :key="day.date.toString()"
        :class="[
          'calendar-day',
          {
            'current-month': day.isCurrentMonth,
            'selected': isSelected(day.date),
            'today': isToday(day.date)
          }
        ]"
        @click="selectDay(day)"
      >
        {{ day.dayNumber }}
      </div>
    </div>
    
    <div v-if="selectedDay" class="selected-info">
      Выбранный день: {{ formattedSelectedDate }}
    </div>
  </div>
</template>

<style scoped>
.calendar {
  max-width: 400px;
  margin: 20px auto;
  font-family: Arial, sans-serif;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding: 10px;
  background-color: #f5f5f5;
  border-radius: 8px;
}

.calendar-header button {
  background: #007bff;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

.calendar-header button:hover {
  background: #0056b3;
}

.calendar-header h2 {
  margin: 0;
  text-transform: capitalize;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 4px;
}

.weekday {
  text-align: center;
  font-weight: bold;
  padding: 10px;
  background-color: #e9ecef;
  border-radius: 4px;
}

.calendar-day {
  text-align: center;
  padding: 12px;
  border: 1px solid #dee2e6;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}

.calendar-day:hover {
  background-color: #f8f9fa;
  transform: scale(1.05);
}

.calendar-day.current-month {
  background-color: white;
}

.calendar-day:not(.current-month) {
  background-color: #f8f9fa;
  color: #6c757d64;
}

.calendar-day.selected {
  background-color: #007bff;
  color: white;
  border-color: #0056b3;
}

.calendar-day.today {
  border: 2px solid #28a745;
  font-weight: bold;
}

.calendar-day.today.selected {
  border-color: #0056b3;
}

.selected-info {
  margin-top: 20px;
  padding: 15px;
  background-color: #e7f3ff;
  border-radius: 8px;
  text-align: center;
  font-weight: bold;
}
</style>