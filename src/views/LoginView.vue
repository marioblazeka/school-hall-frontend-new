<template>
  <div class="flex flex-col justify-center flex-grow max-w-xs mx-auto w-full px-4">
    <!-- Back button -->
    <button @click="$router.push('/')" class="absolute top-4 left-4 text-gray-600 hover:text-[#EE7D31] transition-colors">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="w-6 h-6">
        <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5L8.25 12l7.5-7.5" />
      </svg>
    </button>

    <div class="text-center mb-8">
      <img src="@/assets/logo.png" alt="Logo" class="h-20 mx-auto mb-3" />
      <h3 class="text-lg font-bold text-gray-800 uppercase tracking-wider">{{ authMode === 'login' ? 'Login' : 'Sign up' }}</h3>
    </div>

    <div class="flex border-b border-gray-200 mb-5">
      <button type="button" @click="switchMode('login')" class="flex-1 pb-2 text-sm font-semibold" :class="authMode === 'login' ? 'text-[#EE7D31] border-b-2 border-[#EE7D31]' : 'text-gray-400'">Login</button>
      <button type="button" @click="switchMode('signup')" class="flex-1 pb-2 text-sm font-semibold" :class="authMode === 'signup' ? 'text-[#EE7D31] border-b-2 border-[#EE7D31]' : 'text-gray-400'">Sign up</button>
    </div>

    <!-- Error message -->
    <div v-if="error" class="mb-4 p-3 bg-red-50 border border-red-200 rounded-lg text-red-700 text-sm font-medium">
      {{ error }}
    </div>

    <form @submit.prevent="handleSubmit" class="space-y-4">
      <div class="relative shadow-sm">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <IconUser />
        </span>
        <input v-model="email" type="email" placeholder="Korisničko ime ili Email" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <div class="relative shadow-sm">
        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-gray-400">
          <svg fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z" />
          </svg>
        </span>
        <input v-model="password" type="password" placeholder="Lozinka" class="w-full pl-11 pr-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />
      </div>

      <input v-if="authMode === 'signup'" v-model="confirmPassword" type="password" placeholder="Potvrdi lozinku" minlength="6" class="w-full px-4 py-3.5 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-[#EE7D31] focus:bg-white transition-all text-gray-700" required />

        <button type="submit" :disabled="isLoading" class="w-full bg-[#EE7D31] hover:bg-orange-600 disabled:bg-orange-300 text-white py-3.5 rounded-xl font-bold shadow-md hover:shadow-lg transition-all">
          {{ isLoading ? 'Učitavanje...' : (authMode === 'login' ? 'Sign in' : 'Sign up') }}
        </button>
    </form>

      <button v-if="authMode === 'login'" type="button" @click="showForgotPassword = !showForgotPassword" class="mt-4 text-sm text-gray-500 hover:text-[#EE7D31] underline underline-offset-2 transition-colors">
        Zaboravljena lozinka?
      </button>

    <div v-if="showForgotPassword" class="mt-4 p-4 bg-orange-50 border border-orange-100 rounded-lg">
      <p class="text-sm font-semibold text-gray-700 mb-3">Resetiranje lozinke</p>
      <form v-if="!resetToken" @submit.prevent="requestReset" class="space-y-3">
        <input v-model="resetEmail" type="email" placeholder="Vaš email" class="w-full px-3 py-2.5 bg-white border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-[#EE7D31]" required />
        <button type="submit" :disabled="isResetLoading" class="w-full bg-gray-800 hover:bg-gray-700 disabled:bg-gray-400 text-white py-2.5 rounded-lg text-sm font-semibold">
          {{ isResetLoading ? 'Slanje...' : 'Pošalji zahtjev' }}
        </button>
      </form>

      <form v-else @submit.prevent="resetPassword" class="space-y-3">
        <p class="text-xs text-gray-600">Razvojni token je prikazan ispod. U produkciji bi stigao emailom.</p>
        <input v-model="resetToken" type="text" placeholder="Reset token" class="w-full px-3 py-2.5 bg-white border border-gray-200 rounded-lg text-xs" required />
        <input v-model="newPassword" type="password" placeholder="Nova lozinka (min. 6 znakova)" minlength="6" class="w-full px-3 py-2.5 bg-white border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-[#EE7D31]" required />
        <button type="submit" :disabled="isResetLoading" class="w-full bg-gray-800 hover:bg-gray-700 disabled:bg-gray-400 text-white py-2.5 rounded-lg text-sm font-semibold">
          {{ isResetLoading ? 'Spremanje...' : 'Postavi novu lozinku' }}
        </button>
      </form>
      <p v-if="resetMessage" class="mt-3 text-xs text-green-700">{{ resetMessage }}</p>
      <p v-if="resetError" class="mt-3 text-xs text-red-700">{{ resetError }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import IconUser from '../components/icons/IconUser.vue';

const email = ref('');
const password = ref('');
const confirmPassword = ref('');
const authMode = ref('login');
const router = useRouter();
const isLoading = ref(false);
const error = ref('');
const showForgotPassword = ref(false);
const resetEmail = ref('');
const resetToken = ref('');
const newPassword = ref('');
const resetMessage = ref('');
const resetError = ref('');
const isResetLoading = ref(false);

const switchMode = (mode) => {
  authMode.value = mode;
  error.value = '';
  showForgotPassword.value = false;
  confirmPassword.value = '';
};

const handleLogin = async () => {
  isLoading.value = true;
  error.value = '';
  try {
    const res = await axios.post('https://school-hall-api.onrender.com/api/auth/login', {
      email: email.value,
      password: password.value
    });
    localStorage.setItem('token', res.data.token);
    setTimeout(() => router.push('/admin'), 300);
  } catch (err) {
    error.value = err.response?.data?.msg || 'Prijava neuspješna. Provjerite podatke.';
  } finally {
    isLoading.value = false;
  }
};

const handleSignup = async () => {
  error.value = '';
  if (password.value !== confirmPassword.value) {
    error.value = 'Lozinke se ne podudaraju.';
    return;
  }

  isLoading.value = true;
  try {
    await axios.post('https://school-hall-api.onrender.com/api/auth/signup', {
      email: email.value,
      password: password.value
    });
    authMode.value = 'login';
    password.value = '';
    confirmPassword.value = '';
    error.value = 'Račun je kreiran. Sada se možete prijaviti.';
  } catch (err) {
    error.value = err.response?.data?.msg || 'Registracija neuspješna.';
  } finally {
    isLoading.value = false;
  }
};

const handleSubmit = () => authMode.value === 'login' ? handleLogin() : handleSignup();

const requestReset = async () => {
  isResetLoading.value = true;
  resetMessage.value = '';
  resetError.value = '';
  try {
    const response = await axios.post('https://school-hall-api.onrender.com/api/auth/forgot-password', {
      email: resetEmail.value
    });
    resetMessage.value = response.data.resetToken
      ? `Token: ${response.data.resetToken}`
      : response.data.msg;
    if (response.data.resetToken) resetToken.value = response.data.resetToken;
  } catch (err) {
    resetError.value = err.response?.data?.msg || 'Zahtjev nije moguće obraditi.';
  } finally {
    isResetLoading.value = false;
  }
};

const resetPassword = async () => {
  isResetLoading.value = true;
  resetMessage.value = '';
  resetError.value = '';
  try {
    const response = await axios.post('https://school-hall-api.onrender.com/api/auth/reset-password', {
      token: resetToken.value,
      password: newPassword.value
    });
    resetMessage.value = response.data.msg;
    resetToken.value = '';
    newPassword.value = '';
    showForgotPassword.value = false;
  } catch (err) {
    resetError.value = err.response?.data?.msg || 'Lozinku nije moguće promijeniti.';
  } finally {
    isResetLoading.value = false;
  }
};
</script>
