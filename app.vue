<template>
  <div class="min-h-screen bg-[#0F172A] text-gray-100 font-sans p-10 flex justify-center">
    <div class="max-w-5xl w-full space-y-6">
      <header>
        <h1 class="text-3xl font-bold bg-gradient-to-r from-blue-400 to-emerald-400 bg-clip-text text-transparent">
          Cockpit Agent Integration Starter
        </h1>
        <p class="text-gray-400 mt-2">A drop-in boilerplate to visualize and use Cockpit Tools accounts.</p>
      </header>

      <section class="bg-[#1E293B] rounded-2xl p-6 border border-white/5">
        <div class="flex items-center justify-between mb-6">
          <h2 class="text-lg font-semibold flex items-center gap-2">
            <span class="w-2.5 h-2.5 rounded-full" :class="cockpitStatus.available ? 'bg-emerald-500 shadow-[0_0_10px_rgba(16,185,129,0.5)]' : 'bg-red-500'"></span>
            API Status: Cockpit Tools
          </h2>
        </div>

        <div v-if="cockpitStatus.available" class="space-y-4">
          <p class="text-sm text-gray-400">Account Cockpit Disponibili ({{ cockpitStatus.accounts.length }}):</p>
          
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
            <div v-for="acc in cockpitStatus.accounts" :key="acc.id" 
                 @click="selectedAccountId = acc.id"
                 class="relative p-5 rounded-2xl border transition-all cursor-pointer flex flex-col gap-4"
                 :class="selectedAccountId === acc.id ? 'bg-blue-500/10 border-blue-500/50 shadow-[0_0_20px_rgba(59,130,246,0.1)]' : 'bg-[#0F172A] border-white/5 hover:border-white/20'">
              
              <!-- Intestazione Card -->
              <div class="flex justify-between items-start">
                <div class="flex items-center gap-2">
                  <div class="w-2.5 h-2.5 rounded-full" :class="selectedAccountId === acc.id ? 'bg-blue-500' : 'bg-white/20'"></div>
                  <span class="text-sm font-semibold text-gray-200 truncate max-w-[150px]" :title="acc.email">{{ acc.email.split('@')[0] }}</span>
                </div>
                <span class="text-[10px] font-bold px-2.5 py-1 rounded-full uppercase tracking-wider" 
                      :class="acc.tier === 'PRO' ? 'bg-emerald-500/20 text-emerald-400 border border-emerald-500/30' : 'bg-white/5 text-gray-500 border border-white/10'">
                  {{ acc.tier }}
                </span>
              </div>

              <!-- Quote Grid -->
              <div class="grid grid-cols-2 gap-6 mt-1">
                <!-- Colonna Claude -->
                <div class="space-y-3">
                  <h5 class="text-xs font-bold text-gray-400">Claude</h5>
                  <!-- Claude 5h -->
                  <div v-if="getModelQuota(acc, '3p-5h')" class="space-y-1.5">
                    <div class="flex justify-between text-[10px] font-medium">
                      <span class="text-gray-500">5h</span>
                      <span :class="getModelQuota(acc, '3p-5h').percentage > 10 ? 'text-emerald-400' : 'text-red-400'">{{ getModelQuota(acc, '3p-5h').percentage }}%</span>
                    </div>
                    <div class="h-1.5 bg-black/40 rounded-full overflow-hidden">
                      <div class="h-full rounded-full transition-all duration-500" 
                           :class="getModelQuota(acc, '3p-5h').percentage > 10 ? 'bg-emerald-500' : 'bg-red-500'" 
                           :style="`width: ${getModelQuota(acc, '3p-5h').percentage}%`"></div>
                    </div>
                    <div class="text-[9px] text-gray-500 pt-0.5 whitespace-nowrap overflow-hidden text-ellipsis">
                      {{ formatDate(getModelQuota(acc, '3p-5h').reset_time) }}
                    </div>
                  </div>
                  
                  <!-- Claude Weekly -->
                  <div v-if="getModelQuota(acc, '3p-weekly')" class="space-y-1.5 pt-1">
                    <div class="flex justify-between text-[10px] font-medium">
                      <span class="text-gray-500">Weekly</span>
                      <span :class="getModelQuota(acc, '3p-weekly').percentage > 10 ? 'text-yellow-400' : 'text-red-400'">{{ getModelQuota(acc, '3p-weekly').percentage }}%</span>
                    </div>
                    <div class="h-1.5 bg-black/40 rounded-full overflow-hidden">
                      <div class="h-full rounded-full transition-all duration-500" 
                           :class="getModelQuota(acc, '3p-weekly').percentage > 10 ? 'bg-yellow-500' : 'bg-red-500'" 
                           :style="`width: ${getModelQuota(acc, '3p-weekly').percentage}%`"></div>
                    </div>
                    <div class="text-[9px] text-gray-500 pt-0.5 whitespace-nowrap overflow-hidden text-ellipsis">
                      {{ formatDate(getModelQuota(acc, '3p-weekly').reset_time) }}
                    </div>
                  </div>
                </div>

                <!-- Colonna Gemini -->
                <div class="space-y-3">
                  <h5 class="text-xs font-bold text-gray-400">Gemini</h5>
                  <!-- Gemini 5h -->
                  <div v-if="getModelQuota(acc, 'gemini-5h')" class="space-y-1.5">
                    <div class="flex justify-between text-[10px] font-medium">
                      <span class="text-gray-500">5h</span>
                      <span :class="getModelQuota(acc, 'gemini-5h').percentage > 10 ? 'text-emerald-400' : 'text-red-400'">{{ getModelQuota(acc, 'gemini-5h').percentage }}%</span>
                    </div>
                    <div class="h-1.5 bg-black/40 rounded-full overflow-hidden">
                      <div class="h-full rounded-full transition-all duration-500" 
                           :class="getModelQuota(acc, 'gemini-5h').percentage > 10 ? 'bg-emerald-500' : 'bg-red-500'" 
                           :style="`width: ${getModelQuota(acc, 'gemini-5h').percentage}%`"></div>
                    </div>
                    <div class="text-[9px] text-gray-500 pt-0.5 whitespace-nowrap overflow-hidden text-ellipsis">
                      {{ formatDate(getModelQuota(acc, 'gemini-5h').reset_time) }}
                    </div>
                  </div>
                  
                  <!-- Gemini Weekly -->
                  <div v-if="getModelQuota(acc, 'gemini-weekly')" class="space-y-1.5 pt-1">
                    <div class="flex justify-between text-[10px] font-medium">
                      <span class="text-gray-500">Weekly</span>
                      <span :class="getModelQuota(acc, 'gemini-weekly').percentage > 10 ? 'text-emerald-400' : 'text-red-400'">{{ getModelQuota(acc, 'gemini-weekly').percentage }}%</span>
                    </div>
                    <div class="h-1.5 bg-black/40 rounded-full overflow-hidden">
                      <div class="h-full rounded-full transition-all duration-500" 
                           :class="getModelQuota(acc, 'gemini-weekly').percentage > 10 ? 'bg-emerald-500' : 'bg-red-500'" 
                           :style="`width: ${getModelQuota(acc, 'gemini-weekly').percentage}%`"></div>
                    </div>
                    <div class="text-[9px] text-gray-500 pt-0.5 whitespace-nowrap overflow-hidden text-ellipsis">
                      {{ formatDate(getModelQuota(acc, 'gemini-weekly').reset_time) }}
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="text-sm text-gray-400 p-4 bg-[#0F172A] rounded-xl border border-white/5">
          Nessun account Cockpit rilevato. Assicurati che il demone sia in esecuzione o che i volumi Docker siano mappati correttamente.
        </div>
      </section>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const cockpitStatus = ref({ available: false, accounts: [] as any[] })
const selectedAccountId = ref('')

function getModelQuota(acc: any, modelName: string) {
  if (!acc.quota) return null
  return acc.quota.find((m: any) => m.name === modelName)
}

function formatDate(isoStr: string) {
  if (!isoStr) return ''
  const d = new Date(isoStr)
  if (isNaN(d.getTime())) return isoStr
  return `${String(d.getMonth()+1).padStart(2,'0')}/${String(d.getDate()).padStart(2,'0')} ${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`
}

onMounted(async () => {
  try {
    const res = await $fetch('/api/cockpit')
    if (res && res.data) {
      cockpitStatus.value = res.data
      if (res.data.accounts.length > 0) {
        selectedAccountId.value = res.data.accounts[0].id
      }
    }
  } catch (e) {
    console.error("Cockpit Tools check failed", e)
  }
})
</script>
