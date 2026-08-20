<template>
  <div class="min-h-screen bg-gray-50 flex flex-col items-center justify-between pb-24">
    <div class="w-full max-w-md bg-white min-h-screen shadow-lg relative flex flex-col p-6">
      <router-view />
    </div>

    <!-- Prilagođeni Tab Bar s uvezenim ikonama komponenata -->
    <nav class="fixed bottom-0 w-full max-w-md bg-white border-t border-gray-100 flex justify-around py-2 z-50 shadow-lg">
      
      <router-link to="/" class="flex flex-col items-center justify-center w-20 pt-1 pb-1 transition-colors" :class="[$route.path === '/' ? 'text-[#EE7D31]' : 'text-gray-400']">
        <IconDvorana />
        <span class="text-[11px] font-medium mt-1 select-none">Dvorana</span>
      </router-link>

      <router-link to="/rezervacija" class="flex flex-col items-center justify-center w-20 pt-1 pb-1 transition-colors" :class="[$route.path === '/rezervacija' ? 'text-[#EE7D31]' : 'text-gray-400']">
        <IconRezervacija />
        <span class="text-[11px] font-medium mt-1 select-none">Rezervacija</span>
      </router-link>

      <router-link to="/admin" class="flex flex-col items-center justify-center w-20 pt-1 pb-1 transition-colors" :class="[$route.path.startsWith('/admin') ? 'text-[#EE7D31]' : 'text-gray-400']">
        <IconAdmin />
        <span class="text-[11px] font-medium mt-1 select-none">Administrator</span>
      </router-link>

    </nav>
  </div>
</template>

<script setup>
import IconDvorana from './components/icons/IconDvorana.vue';
import IconRezervacija from './components/icons/IconRezervacija.vue';
import IconAdmin from './components/icons/IconAdmin.vue';
</script>
