<template>
  <div class="flex flex-col items-center flex-grow">
    <!-- Logotip i Naslov -->
    <img src="@/assets/logo.png" alt="School Hall Logo" class="h-20 object-contain mb-2" />
    <h2 class="text-lg font-bold text-gray-800 uppercase tracking-wider mb-4 select-none">Dostupne dvorane</h2>
    
    <!-- Prikaz kalendara iz Figme -->
    <div class="w-full border border-gray-100 rounded-2xl p-4 bg-gray-50 mb-6 shadow-sm">
    <!-- Navigacija mjeseca -->
      <div class="flex justify-between items-center mb-4 font-semibold text-gray-700 select-none">
        <button @click="previousMonth" class="hover:text-[#EE7D31] px-2 transition-colors text-lg">‹</button>
        <span class="text-sm font-bold">{{ monthName }} {{ currentYear }}</span>
        <button @click="nextMonth" class="hover:text-[#EE7D31] px-2 transition-colors text-lg">›</button>
      </div>
      
      <!-- Dani u tjednu -->
      <div class="grid grid-cols-7 gap-1 text-center text-xs text-gray-400 font-bold mb-2 select-none">
        <span>P</span><span>U</span><span>S</span><span>Č</span><span>P</span><span>S</span><span>N</span>
      </div>
      
      <!-- Kalendar -->
      <div class="grid grid-cols-7 gap-1 text-center text-sm">
        <div v-for="emptyDay in firstDayOfMonth" :key="'empty-' + emptyDay" class="p-2 text-gray-300"></div>
        <div 
          v-for="day in daysInMonth" 
          :key="day" 
          @click="selectDate(day)"
          class="relative p-2 rounded-lg cursor-pointer transition-all duration-200 select-none text-gray-600 hover:bg-orange-100 border border-transparent" 
          :class="{
            'bg-[#EE7D31] text-white font-bold shadow-sm border-[#EE7D31]': day === selectedDay,
            'text-gray-400': isDatePassed(day)
          }"
        >
          {{ day }}
          <span v-if="isDateOccupied(day)" class="absolute bottom-0.5 left-1/2 -translate-x-1/2 w-1.5 h-1.5 rounded-full" :class="day === selectedDay ? 'bg-white' : 'bg-red-500'" aria-label="Zauzet termin"></span>
        </div>
      </div>
      <div class="flex items-center justify-center gap-2 mt-3 text-xs text-gray-500">
        <span class="w-1.5 h-1.5 rounded-full bg-red-500"></span>
        <span>Zauzet termin</span>
      </div>
    </div>

    <!-- Status ekrana prema dizajnu (zamijenjen emoji s čistim SVG-om) -->
    <div class="flex flex-col items-center my-auto py-6 text-center">
      <!-- SVG Vektor Kalendara s točnom bojom iz dizajna -->
      <div class="text-[#EE7D31] mb-4 p-4 bg-orange-50 rounded-full">
        <svg fill="none" viewBox="0 0 24 24" stroke-width="1.8" stroke="currentColor" class="w-14 h-14">
          <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 012.25-2.25h13.5A2.25 2.25 0 0121 7.5v11.25m-18 0A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75m-18 0v-7.5A2.25 2.25 0 015.25 9h13.5A2.25 2.25 0 0121 11.25v7.5m-9-6h.008v.008H12v-.008zM12 15h.008v.008H12V15zm0 2.25h.008v.008H12v-.008zM9.75 15h.008v.008H9.75V15zm0 2.25h.008v.008H9.75v-.008zM7.5 15h.008v.008H7.5V15zm0 2.25h.008v.008H7.5v-.008zm6.75-4.5h.008v.008h-.008v-.008zm0 2.25h.008v.008h-.008V15zm0 2.25h.008v.008h-.008v-.008zm2.25-4.5h.008v.008H16.5v-.008zm0 2.25h.008v.008H16.5V15z" />
        </svg>
      </div>
      
      <p class="text-xl font-extrabold text-gray-800 tracking-tight">{{ reservationCount }} rezervacija</p>
      <p class="text-green-600 font-bold text-sm uppercase mt-1 tracking-wider" v-if="reservationCount === 0">Termin je slobodan</p>
      <p class="text-red-600 font-bold text-sm uppercase mt-1 tracking-wider" v-else>Zauzet termin</p>
      <p class="text-xs text-gray-400 mt-2 max-w-[200px]">Odaberi datum {{ selectedDay ? selectedDay + '. ' + monthName + ' ' + currentYear : 'u kalendaru' }}</p>
      <p v-if="availabilityError" class="text-xs text-red-500 mt-2">{{ availabilityError }}</p>
    </div>

    <!-- Akcijski gumb usklađen s formom -->
    <button @click="$router.push('/rezervacija')" class="w-full bg-[#EE7D31] hover:bg-orange-600 text-white py-4 rounded-xl font-bold shadow-md hover:shadow-lg transition-all duration-200 mt-auto tracking-wide">
      + Nova rezervacija
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';

const now = new Date();
const currentMonth = ref(now.getMonth());
const currentYear = ref(now.getFullYear());
const selectedDay = ref(now.getDate());
const reservationCount = ref(0);
const reservationCounts = ref({});
const availabilityError = ref('');

const monthNames = ['Siječanj', 'Veljača', 'Ožujak', 'Travanj', 'Svibanj', 'Lipanj', 'Srpanj', 'Kolovoz', 'Rujan', 'Listopad', 'Studeni', 'Prosinac'];

const monthName = computed(() => monthNames[currentMonth.value]);

const daysInMonth = computed(() => {
  return new Date(currentYear.value, currentMonth.value + 1, 0).getDate();
});

const firstDayOfMonth = computed(() => {
  return new Date(currentYear.value, currentMonth.value, 1).getDay();
});

const isDatePassed = (day) => {
  const checkDate = new Date(currentYear.value, currentMonth.value, day);
  const today = new Date();
  today.setHours(0, 0, 0, 0);
  return checkDate < today;
};

const dateKey = (day) => {
  const month = String(currentMonth.value + 1).padStart(2, '0');
  return `${currentYear.value}-${month}-${String(day).padStart(2, '0')}`;
};

const isDateOccupied = (day) => Boolean(reservationCounts.value[dateKey(day)]);

const updateReservationCount = (day) => {
  reservationCount.value = day ? (reservationCounts.value[dateKey(day)] || 0) : 0;
};

const selectDate = (day) => {
  if (!isDatePassed(day)) {
    selectedDay.value = day;
    updateReservationCount(day);
  }
};

const previousMonth = () => {
  if (currentMonth.value === 0) {
    currentMonth.value = 11;
    currentYear.value--;
  } else {
    currentMonth.value--;
  }
  selectedDay.value = null;
  reservationCount.value = 0;
};

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentMonth.value = 0;
    currentYear.value++;
  } else {
    currentMonth.value++;
  }
  selectedDay.value = null;
  reservationCount.value = 0;
};

const loadAvailability = async () => {
  try {
    const response = await axios.get('http://localhost:5000/api/reservations/availability');
    reservationCounts.value = response.data;

    const todayKey = new Date().toISOString().slice(0, 10);
    const nextOccupiedDate = Object.keys(reservationCounts.value)
      .filter((date) => date >= todayKey)
      .sort()[0];

    if (nextOccupiedDate && !Object.keys(reservationCounts.value).some((date) => {
      const [year, month] = date.split('-');
      return Number(year) === currentYear.value && Number(month) === currentMonth.value + 1;
    })) {
      const [year, month, day] = nextOccupiedDate.split('-').map(Number);
      currentYear.value = year;
      currentMonth.value = month - 1;
      selectedDay.value = day;
    }

    updateReservationCount(selectedDay.value);
  } catch (err) {
    availabilityError.value = 'Podaci o zauzetim terminima trenutno nisu dostupni.';
  }
};

onMounted(loadAvailability);
</script>
