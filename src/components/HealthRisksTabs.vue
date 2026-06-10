<template>
  <div class="form-wrapper">
    <!-- Overlay: only rendered while loading -->
    <div class="overlay" v-if="isLoading">
      <div class="spinner" />
      <span class="overlay-text">Analyzing risk factors…</span>
    </div>

    <div class="card">

      <!-- ── Tabs Header ── -->
      <div class="tabs-header">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          type="button"
          class="tab-button"
          :class="{ active: activeTab === tab.id }"
          @click="activeTab = tab.id; console.log(activeTab)"
        >
          {{ tab.name }}
        </button>
      </div>

      <!-- ── Tab Content ── -->
      <div class="tab-content">

        <FormTab
          v-show="activeTab === 'form'"
          v-model:form="form"
          :result="result"
          @submit="handleSubmit"
        />

        <AiTab
          v-show="activeTab === 'ai'"
          :AIResult="AIresult"
        />

        <StatisticsTab
          v-show="activeTab === 'statistics'"
          :mortalityResult="mortalityResult"
        />

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from "vue"
import FormTab from "./FormTab.vue"
import AiTab from "./AITab.vue"
import StatisticsTab from "./MortalityTab.vue"

// ── Tabs Setup ──
const activeTab = ref("form")
const tabs = [
  { id: "form",       name: "Form"       },
  { id: "ai",         name: "AI"         },
  { id: "statistics", name: "Statistics" },
]

// ── Shared form state (passed down as props) ──
const form = reactive({
  age:          45,
  sbp:          120,
  dbp:          80,
  bmi:          25.0,
  total_chol:   200,
  hdl:          55,
  hba1c:        5.5,
  female:       false,
  diabetes_dx:  false,
  smoking:      false,
  family_history: false,
  // Advanced / optional
  pulse:         72,
  waist:         88,
  non_hdl:       null,
  sedentary_min: 360,
  sleep_hours:   7,
  education:     null,
  income:        null,
  race:          null,
})

const result    = ref(null)
const AIresult  = ref(null)
const mortalityResult = ref(null)
const isLoading = ref(false)

async function handlePost(payload, url) {
    const response = await fetch("http://localhost:8000/" + url, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(payload),
    })

    const text = await response.text()
    if (!response.ok) {
      console.error("Backend rejected request:", text)
      return null;
    }
    return JSON.parse(text);
}

// ── Submit handler lives here so the result is available to all tabs ──
async function handleSubmit() {
  const raw = {
    age:            form.age,
    female:         form.female       ? 1 : 0,
    sbp:            form.sbp,
    dbp:            form.dbp,
    bmi:            form.bmi,
    total_chol:     form.total_chol,
    hdl:            form.hdl,
    hba1c:          form.hba1c,
    diabetes_dx:    form.diabetes_dx  ? 1 : 0,
    smoking:        form.smoking      ? 1 : 0,
    family_history: form.family_history ? 1 : 0,
    pulse:          form.pulse,
    waist:          form.waist,
    non_hdl:        form.non_hdl,
    sedentary_min:  form.sedentary_min,
    sleep_hours:    form.sleep_hours,
    education:      form.education,
    income:         form.income,
    race:           form.race,
  }

  // Strip nulls/undefined so the backend imputes them
  const payload = Object.fromEntries(
    Object.entries(raw).filter(([, v]) => v !== null && v !== undefined)
  )

  try {
    isLoading.value = true
    console.log(payload)
    result.value = await handlePost(payload, "predict/explain");
    mortalityResult.value = await handlePost(payload, "mortality/predict");
    AIresult.value = await handlePost(payload, "predict/suggestions");

    console.log(result.value)
    console.log(mortalityResult.value)
    console.log(AIresult.value)
    
  } catch (err) {
    console.error("Request failed:", err)
  } finally {
    isLoading.value = false
  }
}
</script>

<style scoped>
* {
  font-family: monospace;
}

.form-wrapper {
  position: relative;
  width: 100%;
  max-width: 800px;
}

.overlay {
  position: absolute;
  inset: 0;
  background: rgba(255, 255, 255, 0.75);
  backdrop-filter: blur(3px);
  border-radius: 16px;
  z-index: 10;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 14px;
}

.overlay-text {
  font-size: 14px;
  font-weight: 500;
  color: #4f46e5;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 40px;
  height: 40px;
  border: 3.5px solid rgba(99, 102, 241, 0.2);
  border-top-color: #4f46e5;
  border-radius: 50%;
  animation: spin 0.75s linear infinite;
}

.card {
  width: 100%;
  max-width: 800px;
  background: white;
  padding: 28px;
  border-radius: 16px;
  box-sizing: border-box;
  box-shadow: 0 12px 30px rgba(0, 0, 0, 0.08);
}

.tabs-header {
  display: flex;
  gap: 8px;
  border-bottom: 1px solid #e5e7eb;
  margin-bottom: 24px;
  padding-bottom: 8px;
}

.tab-button {
  background: transparent;
  border: none;
  padding: 10px 18px;
  font-size: 14px;
  font-weight: 600;
  color: #6b7280;
  cursor: pointer;
  border-radius: 8px;
  transition: all 0.2s;
  font-family: monospace;
}

.tab-button:hover {
  background: #f3f4f6;
  color: #374151;
}

.tab-button.active {
  background: #eef2ff;
  color: #4f46e5;
}
</style>