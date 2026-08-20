<template>
  <div class="flex flex-col items-center flex-grow">
    <!-- Logotip i Naslov -->
    <img src="@/assets/logo.png" alt="School Hall Logo" class="h-20 object-contain mb-4 animate-fade-in" />
    <h2 class="text-xl font-bold text-gray-800 uppercase tracking-wider mb-2 select-none">School Hall Booking</h2>
    
    <!-- Prikaz kalendara iz Figme -->
    <div class="w-full border border-gray-100 rounded-2xl p-4 bg-gray-50 mb-6 shadow-sm">
      <div class="flex justify-between items-center mb-4 font-semibold text-gray-700 select-none">
        <button class="hover:text-[#EE7D31] px-2 transition-colors">‹</button>
        <span class="text-sm">Rujan 2026</span>
        <button class="hover:text-[#EE7D31] px-2 transition-colors">›</button>
      </div>
      <div class="grid grid-cols-7 gap-2 text-center text-xs text-gray-400 font-bold mb-2 select-none">
        <span>Su</span><span>Mo</span><span>Tu</span><span>We</span><span>Th</span><span>Fr</span><span>Sa</span>
      </div>
      <div class="grid grid-cols-7 gap-2 text-center text-sm">
        <div 
          v-for="day in 30" 
          :key="day" 
          class="p-2 rounded-full cursor-pointer transition-all duration-200 select-none text-gray-600 hover:bg-orange-50" 
          :class="{'bg-[#EE7D31] text-white font-bold shadow-sm hover:bg-[#EE7D31]': day === 15}"
        >
          {{ day }}
        </div>
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
      
      <p class="text-xl font-extrabold text-gray-800 tracking-tight">0 rezervacija</p>
      <p class="text-[#EE7D31] font-bold text-sm uppercase mt-1 tracking-wider">Termin je slobodan</p>
      <p class="text-xs text-gray-400 mt-2 max-w-[200px]">Nema unesenih zahtjeva za odabrani datum u sustavu</p>
    </div>

    <!-- Akcijski gumb usklađen s formom -->
    <button @click="$router.push('/rezervacija')" class="w-full bg-[#EE7D31] hover:bg-orange-600 text-white py-4 rounded-xl font-bold shadow-md hover:shadow-lg transition-all duration-200 mt-auto tracking-wide">
      + Nova rezervacija
    </button>
  </div>
</template>

<script setup>
// Nema potrebe za dodatnim uvozom ikona jer su ugrađene izravno kao inline SVG elementi radi maksimalnih performansi.
</script>
