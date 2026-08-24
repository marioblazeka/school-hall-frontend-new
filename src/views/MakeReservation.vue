<template>
  <div class="flex flex-col flex-grow animate-fade-in">
    <!-- Gornje zaglavlje s Logom -->
    <div class="text-center mb-6 select-none">
      <img src="@/assets/logo.png" alt="Logo" class="h-16 mx-auto mb-2" />
      <h3 class="text-sm font-bold text-[#EE7D31] uppercase tracking-wider">Nova rezervacija</h3>
      <p class="text-xs text-gray-400">Zahtjev zahtijeva odobrenje administratora</p>
    </div>

    <!-- Forma za predaju zahtjeva -->
    <form @submit.prevent="handleSubmit" class="space-y-4 flex-grow flex flex-col">
      
      <!-- Polje 1: Ime i prezime -->
      <div class="relative">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <IconUser />
        </span>
        <input v-model="form.fullName" type="text" placeholder="Ime i prezime / Organizacija" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <!-- Polje 2: E-mail -->
      <div class="relative">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <IconEmail />
        </span>
        <input v-model="form.email" type="email" placeholder="E-mail adresa" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <!-- Polje 3: Prebivalište / Sjedište -->
      <div class="relative">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <IconLocation />
        </span>
        <input v-model="form.location" type="text" placeholder="Prebivalište / Sjedište organizacije" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <!-- Polje 4: Vremenski termin -->
      <div class="relative">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <IconClock />
        </span>
        <input v-model="form.timeSlot" type="text" placeholder="Vremenski termin (npr. 18:00 - 19:30)" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <!-- Selektor Dvorane -->
      <div class="relative">
        <select v-model="form.hallName" class="w-full px-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700 appearance-none cursor-pointer">
          <option value="Glavna dvorana">Glavna sportska dvorana</option>
          <option value="Mala dvorana">Mala dvorana (svlačionice)</option>
        </select>
        <span class="absolute inset-y-0 right-0 flex items-center pr-4 text-gray-400 pointer-events-none text-xs">▼</span>
      </div>

      <!-- Sekcija s kvačicama za dodatne resurse -->
      <div class="bg-gray-50 p-4 rounded-xl border border-gray-100 space-y-2.5">
        <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1 select-none">Dodatno potrebni resursi:</p>
        <label class="flex items-center text-sm text-gray-600 cursor-pointer select-none">
          <input type="checkbox" v-model="form.resources.equipment" class="w-4 h-4 rounded text-[#EE7D31] focus:ring-[#EE7D31] border-gray-300 mr-3 accent-[#EE7D31]" />
          Sportska oprema (lopte, mrežice)
        </label>
        <label class="flex items-center text-sm text-gray-600 cursor-pointer select-none">
          <input type="checkbox" v-model="form.resources.lockers" class="w-4 h-4 rounded text-[#EE7D31] focus:ring-[#EE7D31] border-gray-300 mr-3 accent-[#EE7D31]" />
          Korištenje dodatnih svlačionica
        </label>
        <label class="flex items-center text-sm text-gray-600 cursor-pointer select-none">
          <input type="checkbox" v-model="form.resources.techDevices" class="w-4 h-4 rounded text-[#EE7D31] focus:ring-[#EE7D31] border-gray-300 mr-3 accent-[#EE7D31]" />
          Tehnički uređaji (razglas, semafor)
        </label>
      </div>

      <!-- Akcijski gumb za podnošenje -->
      <button type="submit" class="w-full bg-[#EE7D31] hover:bg-orange-600 text-white py-4 rounded-xl font-bold shadow-md hover:shadow-lg transition-all duration-200 mt-auto tracking-wide">
        Pošalji zahtjev na odobrenje
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

// Uvoz ikona komponenata
import IconUser from '../components/icons/IconUser.vue';
import IconEmail from '../components/icons/IconEmail.vue';
import IconLocation from '../components/icons/IconLocation.vue';
import IconClock from '../components/icons/IconClock.vue';

const router = useRouter();

const form = ref({
  fullName: '',
  email: '',
  location: '',
  hallName: 'Glavna dvorana',
  date: '2026-09-15', // Fiksirano na selektirani dan iz kalendara
  timeSlot: '',
  resources: { equipment: false, lockers: false, techDevices: false }
});

const handleSubmit = async () => {
  try {
    await axios.post('https://school-hall-api.onrender.com/api/reservations', form.value);
    alert('Zahtjev je uspješno zaprimljen i poslan administratoru na čekanje!');
    router.push('/'); // Vraćanje na početni kalendar
  } catch (err) {
    alert('Došlo je do greške prilikom slanja zahtjeva. Molimo pokušajte ponovno.');
  }
};
</script>
