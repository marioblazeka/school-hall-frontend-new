<template>
  <div class="flex flex-col flex-grow">
    <div class="flex justify-between items-center border-b pb-4 mb-4">
      <div>
        <h3 class="text-lg font-bold text-gray-800">Upravljačka ploča</h3>
        <p class="text-xs text-orange-500 font-semibold">Pregled zahtjeva</p>
      </div>
      <button @click="logout" class="text-xs bg-gray-200 text-gray-600 px-3 py-1.5 rounded-lg hover:bg-gray-300">Odjava</button>
    </div>

    <div class="space-y-4 flex-grow overflow-y-auto">
      <p v-if="error" class="p-3 rounded-lg bg-red-50 border border-red-200 text-sm text-red-700">{{ error }}</p>
      <p v-else-if="!reqs.length" class="p-4 rounded-xl bg-gray-50 text-sm text-gray-500 text-center">Nema zaprimljenih rezervacija.</p>

      <div v-for="item in reqs" :key="item._id" class="p-4 border border-gray-200 rounded-xl bg-gray-50 shadow-sm">
        <div class="flex justify-between items-start mb-2">
          <div>
            <h4 class="font-bold text-sm text-gray-800">{{ item.fullName }}</h4>
            <p class="text-xs text-gray-500">{{ item.email }}</p>
          </div>
          <span class="text-xs px-2 py-1 rounded font-bold" :class="{
            'bg-yellow-100 text-yellow-700': item.status === 'Na čekanju',
            'bg-green-100 text-green-700': item.status === 'Odobreno',
            'bg-red-100 text-red-700': item.status === 'Odbijeno'
          }">{{ item.status }}</span>
        </div>

        <dl class="grid grid-cols-2 gap-x-3 gap-y-2 text-xs mb-3">
          <div>
            <dt class="text-gray-400">Datum</dt>
            <dd class="font-semibold text-gray-700">{{ item.date }}</dd>
          </div>
          <div>
            <dt class="text-gray-400">Termin</dt>
            <dd class="font-semibold text-gray-700">{{ item.timeSlot }}</dd>
          </div>
          <div>
            <dt class="text-gray-400">Lokacija</dt>
            <dd class="font-semibold text-gray-700">{{ item.location }}</dd>
          </div>
          <div>
            <dt class="text-gray-400">Dvorana</dt>
            <dd class="font-semibold text-gray-700">{{ item.hallName || 'Nije navedeno' }}</dd>
          </div>
        </dl>

        <p class="text-xs text-gray-600 mb-3">
          <span class="text-gray-400">Resursi:</span>
          {{ selectedResources(item) }}
        </p>
        <p class="text-xs text-gray-600 italic bg-white p-2 rounded border border-gray-100 mb-3" v-if="item.notes">"{{ item.notes }}"</p>
        
        <div class="flex space-x-2" v-if="item.status === 'Na čekanju'">
          <button @click="updateStatus(item._id, 'Odobreno')" class="flex-1 bg-green-500 text-white text-xs py-2 rounded-lg font-bold hover:bg-green-600">Odobri</button>
          <button @click="updateStatus(item._id, 'Odbijeno')" class="flex-1 bg-red-500 text-white text-xs py-2 rounded-lg font-bold hover:bg-red-600">Odbij</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const reqs = ref([]);
const router = useRouter();
const error = ref('');

const selectedResources = (item) => {
  const resources = [];
  if (item.resources?.equipment) resources.push('sportska oprema');
  if (item.resources?.lockers) resources.push('svlačionice');
  if (item.resources?.techDevices) resources.push('tehnički uređaji');
  return resources.length ? resources.join(', ') : 'Nisu odabrani dodatni resursi';
};

const fetchReqs = async () => {
  try {
    error.value = '';
    const token = localStorage.getItem('token');
    if (!token) {
      router.push('/login');
      return;
    }
    const res = await axios.get('http://localhost:5000/api/reservations', {
      headers: { Authorization: `Bearer ${token}` }
    });
    reqs.value = res.data;
  } catch (err) {
    localStorage.removeItem('token');
    router.push('/login');
  }
};

const updateStatus = async (id, status) => {
  try {
    const token = localStorage.getItem('token');
    await axios.put(`http://localhost:5000/api/reservations/${id}`, { status }, {
      headers: { Authorization: `Bearer ${token}` }
    });
    fetchReqs();
  } catch (err) {
    alert('Greška pri ažuriranju statusa.');
  }
};

const logout = () => {
  localStorage.removeItem('token');
  router.push('/login');
};
onMounted(fetchReqs);
</script>
