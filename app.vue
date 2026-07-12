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

      <!-- Chat & Test Sandbox Section -->
      <section class="bg-[#1E293B] rounded-2xl p-6 border border-white/5 space-y-6">
        <h2 class="text-lg font-semibold flex items-center gap-2 text-gray-200">
          💬 Cockpit AI Sandbox & Multi-Model Testing
        </h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div>
            <label class="block text-xs font-semibold text-blue-400 uppercase tracking-wider mb-1">Modello LLM (Catalogo)</label>
            <select v-model="selectedModel" class="w-full bg-[#0F172A] border border-white/10 rounded-xl p-3 text-sm focus:border-blue-500 outline-none text-gray-200">
              <optgroup v-for="group in modelGroups" :key="group.category" :label="`${group.icon} ${group.label}`">
                <option v-for="m in group.models" :key="m.id" :value="m.id">{{ m.name }} ({{ m.provider }})</option>
              </optgroup>
            </select>
          </div>
          <div>
            <label class="block text-xs font-semibold text-purple-400 uppercase tracking-wider mb-1">Modalità di Ragionamento</label>
            <select v-model="reasoningMode" class="w-full bg-[#0F172A] border border-white/10 rounded-xl p-3 text-sm focus:border-purple-500 outline-none text-gray-200">
              <option value="standard">⚡ Standard / Bilanciato</option>
              <option value="creative">🎨 Creativo / Copywriting</option>
              <option value="analytical">🧠 Analitico / Matematico</option>
              <option value="antiban">🛡️ Anti-Ban Stealth Max (2026)</option>
              <option value="cot">🔍 Chain-of-Thought (&lt;ragionamento&gt;)</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-semibold text-emerald-400 uppercase tracking-wider mb-1">Preset Prompt Professionali</label>
            <select v-model="promptCategory" class="w-full bg-[#0F172A] border border-white/10 rounded-xl p-3 text-sm focus:border-emerald-500 outline-none text-gray-200">
              <option v-for="prompt in PROMPT_INDEX" :key="prompt.id" :value="prompt.id">{{ prompt.name }}</option>
            </select>
          </div>
        </div>

        <div class="bg-[#0F172A] rounded-xl p-4 min-h-[160px] max-h-[300px] overflow-y-auto space-y-3 border border-white/5">
          <div v-for="(msg, idx) in messages" :key="idx" class="p-3 rounded-xl text-sm font-mono whitespace-pre-wrap"
               :class="msg.role === 'user' ? 'bg-blue-600/20 text-blue-100 ml-auto max-w-[85%]' : 'bg-white/5 text-gray-200 max-w-[90%]'">
            <div class="text-[10px] font-bold uppercase tracking-wider mb-1" :class="msg.role === 'user' ? 'text-blue-400' : 'text-emerald-400'">
              {{ msg.role === 'user' ? 'Tu' : 'Cockpit AI' }}
            </div>
            {{ msg.content }}
          </div>
          <p v-if="!messages.length" class="text-xs text-gray-500 text-center py-8">Nessun messaggio. Scrivi qui sotto per testare l'instradamento Cockpit!</p>
        </div>

        <form @submit.prevent="sendMessage" class="flex gap-3">
          <input v-model="input" type="text" placeholder="Scrivi una richiesta per testare il modello e le quote..."
                 class="flex-1 bg-[#0F172A] border border-white/10 rounded-xl p-3.5 text-sm focus:border-blue-500 outline-none text-gray-100" />
          <button type="submit" :disabled="loading || !input"
                  class="bg-blue-600 hover:bg-blue-500 disabled:opacity-50 text-white font-medium px-6 py-3.5 rounded-xl transition-all">
            {{ loading ? '...' : 'Invia' }}
          </button>
        </form>
      </section>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { getModelsGroupedByCategory, LlmModelEntry } from '~/lib/llm-models'
import { PROMPT_INDEX } from '~/lib/prompt-registry'

const cockpitStatus = ref({ available: false, accounts: [] as any[] })
const selectedAccountId = ref('')

const dynamicModels = ref<LlmModelEntry[]>([])
const selectedModel = ref('gpt-4o-mini')
const reasoningMode = ref('standard')
const promptCategory = ref('cockpit-multiaccount-agent')
const input = ref('')
const messages = ref<{role: string, content: string}[]>([])
const loading = ref(false)

const modelGroups = computed(() => getModelsGroupedByCategory('openai', dynamicModels.value.length ? dynamicModels.value : undefined))

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

async function fetchCatalog() {
  try {
    const res = await $fetch<{data: LlmModelEntry[]}>('/api/models')
    if (res && res.data) {
      dynamicModels.value = res.data
      if (res.data.length > 0) selectedModel.value = res.data[0].id
    }
  } catch (e) {}
}

async function sendMessage() {
  if (!input.value.trim() || loading.value) return
  const userText = input.value
  messages.value.push({ role: 'user', content: userText })
  input.value = ''
  loading.value = true

  try {
    const res = await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        prompt: userText,
        accountId: selectedAccountId.value,
        model: selectedModel.value,
        modelOverride: selectedModel.value,
        reasoningMode: reasoningMode.value,
        promptCategory: promptCategory.value
      })
    })

    const reader = res.body?.getReader()
    const decoder = new TextDecoder()
    if (reader) {
      let buffer = ''
      while (true) {
        const { done, value } = await reader.read()
        if (done) break
        buffer += decoder.decode(value, { stream: true })
        const lines = buffer.split('\n\n')
        buffer = lines.pop() || ''
        for (const line of lines) {
          if (line.startsWith('data: ')) {
            try {
              const data = JSON.parse(line.slice(6))
              if (data.type === 'complete') {
                messages.value.push({ role: 'assistant', content: data.result })
              } else if (data.type === 'error') {
                messages.value.push({ role: 'assistant', content: `Error: ${data.error}` })
              }
            } catch (e) {}
          }
        }
      }
    }
  } catch (e: any) {
    messages.value.push({ role: 'assistant', content: `Error: ${e.message}` })
  } finally {
    loading.value = false
  }
}

onMounted(async () => {
  fetchCatalog()
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
