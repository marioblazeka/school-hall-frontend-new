<template>
  <div class="flex flex-col justify-center flex-grow max-w-xs mx-auto w-full">
    <div class="text-center mb-8">
      <img src="@/assets/logo.png" alt="Logo" class="h-20 mx-auto mb-3" />
      <h3 class="text-lg font-bold text-gray-800 uppercase tracking-wider">Administracija</h3>
    </div>

    <form @submit.prevent="handleLogin" class="space-y-4">
      <div class="relative shadow-sm">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-sm">👤</span>
        <input v-model="email" type="email" placeholder="Korisničko ime ili Email" class="w-full pl-10 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none" required />
      </div>

      <div class="relative shadow-sm">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-sm">🔒</span>
        <input v-model="password" type="password" placeholder="Password" class="w-full pl-10 pr-10 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none" required />
        <span class="absolute inset-y-0 right-0 flex items-center pr-3 cursor-pointer text-xs">👁️</span>
      </div>

      <button type="submit" class="w-full bg-orange-500 hover:bg-orange-600 text-white py-3.5 rounded-full font-bold shadow-md transition-all">
        Login
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const email = ref('');
const password = ref('');
const router = useRouter();

const handleLogin = async () => {
  try {
    const res = await axios.post('http://localhost:5000/api/auth/login', {
      email: email.value, password: password.value
    });
    localStorage.setItem('token', res.data.token);
    router.push('/admin/dashboard');
  } catch (err) {
    alert(err.response?.data?.msg || 'Prijava neuspješna.');
  }
};
</script>
