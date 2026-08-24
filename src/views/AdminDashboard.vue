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
      <div v-for="item in reqs" :key="item._id" class="p-4 border border-gray-200 rounded-xl bg-gray-50 shadow-sm">
        <div class="flex justify-between items-start mb-2">
          <div>
            <h4 class="font-bold text-sm text-gray-800">{{ item.fullName }}</h4>
            <p class="text-xs text-gray-500">📍 {{ item.location }} | 🕒 {{ item.timeSlot }}</p>
          </div>
          <span class="text-xs px-2 py-1 rounded font-bold" :class="{
            'bg-yellow-100 text-yellow-700': item.status === 'Na čekanju',
            'bg-green-100 text-green-700': item.status === 'Odobreno',
            'bg-red-100 text-red-700': item.status === 'Odbijeno'
          }">{{ item.status }}</span>
        </div>
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

const fetchReqs = async () => {
  try {
    const token = localStorage.getItem('token');
    const res = await axios.get('http://localhost:5000/api/reservations', {
      headers: { Authorization: `Bearer ${token}` }
    });
    reqs.value = res.data;
  } catch (err) {
    router.push('/admin');
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

const logout = () => { localStorage.removeItem('token'); router.push('/admin'); };
onMounted(fetchReqs);
</script>
