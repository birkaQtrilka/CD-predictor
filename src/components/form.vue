<template>
  <div class="form-wrapper">
  <!-- Overlay: only rendered while loading -->
  <div class="overlay" v-if="isLoading">
    <div class="spinner" />
    <span class="overlay-text">Analyzing risk factors…</span>
  </div>
    <form class="card" @submit.prevent="handleSubmit">

      <h2 class="title">Health Risk Input Form</h2>

      <div class="grid">

        <div class="field">
          <label>Age</label>
          <input v-model.number="form.age" type="number" min="20" required />
        </div>

        <div class="field">
          <label>Female</label>
          <label class="toggle">
            <input type="checkbox" v-model="form.female" />
            <span>{{ form.female ? "Yes" : "No" }}</span>
          </label>
        </div>

        <div class="field">
          <label>Systolic BP</label>
          <input v-model.number="form.sbp" type="number" min="50" required />
        </div>

        <div class="field">
          <label>Diastolic BP</label>
          <input v-model.number="form.dbp" type="number" min="20" required />
        </div>

        <div class="field">
          <label>BMI</label>
          <input v-model.number="form.bmi" type="number" step="0.1" min="10" required />
        </div>

        <div class="field">
          <label>Total Cholesterol</label>
          <input v-model.number="form.total_chol" type="number" min="50" required />
        </div>

        <div class="field">
          <label>HDL</label>
          <input v-model.number="form.hdl" type="number" min="10" required />
        </div>

        <div class="field">
          <label>HbA1c</label>
          <input v-model.number="form.hba1c" type="number" step="0.1" min="2" required />
        </div>

        <div class="field">
          <label>Diabetes Diagnosis</label>
          <label class="toggle">
            <input type="checkbox" v-model="form.diabetes_dx" />
            <span>{{ form.diabetes_dx ? "Yes" : "No" }}</span>
          </label>
        </div>

        <div class="field">
          <label>Smoking</label>
          <label class="toggle">
            <input type="checkbox" v-model="form.smoking" />
            <span>{{ form.smoking ? "Yes" : "No" }}</span>
          </label>
        </div>

        <div class="field">
          <label>Family History</label>
          <label class="toggle">
            <input type="checkbox" v-model="form.family_history" />
            <span>{{ form.family_history ? "Yes" : "No" }}</span>
          </label>
        </div>

      </div>

      <!-- ── Advanced Inputs foldout ── -->
      <div class="advanced-section">
        <button
          type="button"
          class="advanced-toggle"
          @click="showAdvanced = !showAdvanced"
          :aria-expanded="showAdvanced"
        >
          <span class="advanced-toggle-label">
            <svg class="adv-icon" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
              <path fill-rule="evenodd"
                d="M11.49 3.17c-.38-1.56-2.6-1.56-2.98 0a1.532 1.532 0 01-2.286.948c-1.372-.836-2.942.734-2.106 2.106.54.886.061 2.042-.947 2.287-1.561.379-1.561 2.6 0 2.978a1.532 1.532 0 01.947 2.287c-.836 1.372.734 2.942 2.106 2.106a1.532 1.532 0 012.287.947c.379 1.561 2.6 1.561 2.978 0a1.533 1.533 0 012.287-.947c1.372.836 2.942-.734 2.106-2.106a1.533 1.533 0 01.947-2.287c1.561-.379 1.561-2.6 0-2.978a1.532 1.532 0 01-.947-2.287c.836-1.372-.734-2.942-2.106-2.106a1.532 1.532 0 01-2.287-.947zM10 13a3 3 0 100-6 3 3 0 000 6z"
                clip-rule="evenodd" />
            </svg>
            Advanced Inputs
          </span>
          <svg
            class="chevron"
            :class="{ open: showAdvanced }"
            viewBox="0 0 20 20"
            fill="currentColor"
            aria-hidden="true"
          >
            <path fill-rule="evenodd"
              d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
              clip-rule="evenodd" />
          </svg>
        </button>

        <div class="advanced-body" :class="{ expanded: showAdvanced }">
          <p class="advanced-hint">
            These fields are optional. If left at their defaults they will be imputed
            by the model from population statistics.
          </p>
          <div class="grid">

            <div class="field">
              <label>Resting Pulse <span class="unit">(bpm)</span></label>
              <input v-model.number="form.pulse" type="number" min="20" max="300" />
            </div>

            <div class="field">
              <label>Waist Circumference <span class="unit">(cm)</span></label>
              <input v-model.number="form.waist" type="number" min="40" max="250" step="0.1" />
            </div>

            <div class="field">
              <label>Non-HDL Cholesterol <span class="unit">(mg/dL)</span></label>
              <input v-model.number="form.non_hdl" type="number" min="10" max="500"
                placeholder="Auto-derived if blank" />
            </div>

            <div class="field">
              <label>Sedentary Time <span class="unit">(min/day)</span></label>
              <input v-model.number="form.sedentary_min" type="number" min="0" max="1440" />
            </div>

            <div class="field">
              <label>Sleep Duration <span class="unit">(hrs/night)</span></label>
              <input v-model.number="form.sleep_hours" type="number" min="0" max="24" step="0.5" />
            </div>

            <div class="field">
              <label>Education Level <span class="unit">(1–5)</span></label>
              <select v-model.number="form.education">
                <option :value="null">— Not specified —</option>
                <option value="1">1 – Less than 9th grade</option>
                <option value="2">2 – 9th–11th grade</option>
                <option value="3">3 – High school / GED</option>
                <option value="4">4 – Some college / AA</option>
                <option value="5">5 – College graduate</option>
              </select>
            </div>

            <div class="field">
              <label>Income-to-Poverty Ratio</label>
              <input v-model.number="form.income" type="number" min="0" step="0.1"
                placeholder="e.g. 2.5" />
            </div>

            <div class="field">
              <label>Race / Ethnicity</label>
              <select v-model.number="form.race">
                <option :value="null">— Not specified —</option>
                <option value="1">Mexican American</option>
                <option value="2">Other Hispanic</option>
                <option value="3">Non-Hispanic White</option>
                <option value="4">Non-Hispanic Black</option>
                <option value="6">Non-Hispanic Asian</option>
                <option value="7">Other / Multiracial</option>
              </select>
            </div>

          </div>
        </div>
      </div>
      <!-- ── end Advanced Inputs ── -->

      <button class="btn" type="submit">Submit</button>

<div v-if="result" class="results">
  <div class="result-card prob">
    <span class="result-label">CVD Probability</span>
    <span class="result-value">{{ (result.cvd_probability * 100).toFixed(1) }}%</span>
  </div>
  <div class="result-card risk" :class="result.risk_level">
    <span class="result-label">Risk Level</span>
    <span class="result-value capitalize">{{ result.risk_level }}</span>
  </div>
  <div class="result-card full">
    <span class="result-label">Assessed For</span>
    <span class="result-desc">{{ result.label }}</span>
  </div>

  <!-- Explanation section (only rendered when SHAP succeeded) -->
  <template v-if="result.explanation && !result.explanation.error">
    <div class="result-card full drivers">
      <span class="result-label">Top Risk Drivers</span>
      <ul class="factor-list">
        <li v-for="f in result.explanation.top_risk_drivers" :key="f.feature" class="factor risk-factor">
          <span class="factor-name">{{ f.label }}</span>
          <span class="factor-shap">+{{ f.shap.toFixed(3) }}</span>
        </li>
      </ul>
    </div>
    <div class="result-card full protectors">
      <span class="result-label">Top Protective Factors</span>
      <ul class="factor-list">
        <li v-for="f in result.explanation.top_protective_factors" :key="f.feature" class="factor protect-factor">
          <span class="factor-name">{{ f.label }}</span>
          <span class="factor-shap">{{ f.shap.toFixed(3) }}</span>
        </li>
      </ul>
    </div>
    <div class="result-card full">
      <span class="result-label">Note</span>
      <span class="result-desc">{{ result.explanation.note }}</span>
    </div>
  </template>

  <!-- Fallback if SHAP failed server-side -->
  <div v-else-if="result.explanation?.error" class="result-card full">
    <span class="result-label">Explanation Unavailable</span>
    <span class="result-desc">{{ result.explanation.error }}</span>
  </div>
</div>

    </form>
  </div>
</template>

<script setup>
import { ref, computed, reactive } from "vue"

const form = reactive({
  // ── Core fields ──
  age: 45,
  sbp: 120,
  dbp: 80,
  bmi: 25.0,
  total_chol: 200,
  hdl: 55,
  hba1c: 5.5,
  female: false,
  diabetes_dx: false,
  smoking: false,
  family_history: false,

  // ── Advanced / optional fields ──
  pulse: 72,
  waist: 88,
  non_hdl: null,       // auto-derived from total_chol - hdl when null
  sedentary_min: 360,
  sleep_hours: 7,
  education: null,
  income: null,
  race: null,
})

const showAdvanced = ref(false)
const submitted = ref(false)
const result = ref(null)
const isLoading = ref(false)

const isValid = computed(() => {
  return (
    (form.age ?? 0) >= 18 &&
    (form.sbp ?? 0) >= 50 &&
    (form.dbp ?? 0) >= 20 &&
    (form.bmi ?? 0) >= 10 &&
    (form.total_chol ?? 0) >= 50 &&
    (form.hdl ?? 0) >= 10 &&
    (form.hba1c ?? 0) >= 2
  )
})

async function handleSubmit() {
  submitted.value = true

  if (!isValid.value) {
    alert("Please fill all required fields correctly")
    return
  }

  // Build payload — omit null/undefined so the backend imputes them
  const raw = {
    age: form.age,
    female: form.female ? 1 : 0,
    sbp: form.sbp,
    dbp: form.dbp,
    bmi: form.bmi,
    total_chol: form.total_chol,
    hdl: form.hdl,
    hba1c: form.hba1c,
    diabetes_dx: form.diabetes_dx ? 1 : 0,
    smoking: form.smoking ? 1 : 0,
    family_history: form.family_history ? 1 : 0,
    // Advanced
    pulse: form.pulse,
    waist: form.waist,
    non_hdl: form.non_hdl,
    sedentary_min: form.sedentary_min,
    sleep_hours: form.sleep_hours,
    education: form.education,
    income: form.income,
    race: form.race,
  }

  // Strip nulls so the backend treats them as missing
  const payload = Object.fromEntries(
    Object.entries(raw).filter(([, v]) => v !== null && v !== undefined)
  )

  try {
    isLoading.value = true;
    const response = await fetch("http://localhost:8000/predict/explain", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(payload)
    })

    const text = await response.text()
    
    if (!response.ok) {
      console.error("Backend rejected request:", text)
      throw new Error(`HTTP error ${response.status}`)
    }

    const data = JSON.parse(text)

    console.log("Prediction result:", data)
    result.value = data

  } catch (err) {
    console.error("Request failed:", err)
  } finally {
    isLoading.value = false;
  }
}
</script>

<style scoped>
*{
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

/* ── Advanced foldout ── */
.advanced-section {
  margin-top: 24px;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  overflow: hidden;
}

.advanced-toggle {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 13px 16px;
  background: #f8fafc;
  border: none;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  color: #374151;
  transition: background 0.15s;
  gap: 8px;
}

.advanced-toggle:hover {
  background: #f1f5f9;
}

.advanced-toggle-label {
  display: flex;
  align-items: center;
  gap: 8px;
}

.adv-icon {
  width: 16px;
  height: 16px;
  color: #6366f1;
  flex-shrink: 0;
}

.chevron {
  width: 18px;
  height: 18px;
  color: #94a3b8;
  flex-shrink: 0;
  transition: transform 0.25s ease;
}

.chevron.open {
  transform: rotate(180deg);
}

.advanced-body {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.35s ease, padding 0.2s ease;
  padding: 0 16px;
}

.advanced-body.expanded {
  max-height: 600px;
  padding: 16px;
}

.advanced-hint {
  margin: 0 0 14px;
  font-size: 12px;
  color: #94a3b8;
  line-height: 1.5;
}

.unit {
  font-weight: 400;
  color: #9ca3af;
  font-size: 11px;
}

/* ── shared field / grid styles ── */
.factor-list {
  list-style: none;
  padding: 0;
  margin: 8px 0 0;
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.factor {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 14px;
}

.risk-factor {
  background: #fef2f2;
  color: #991b1b;
}

.protect-factor {
  background: #f0fdf4;
  color: #166534;
}

.factor-name {
  font-weight: 500;
}

.factor-shap {
  font-family: monospace;
  font-size: 13px;
  opacity: 0.8;
}

.card {
  width: 100%;
  max-width: 800px;
  background: white;
  padding: 28px;
  border-radius: 16px;
  box-sizing: border-box;
  box-shadow: 0 12px 30px rgba(0,0,0,0.08);
}

.title {
  margin-bottom: 20px;
  font-size: 20px;
  font-weight: 600;
  color: #111827;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

label {
  font-size: 13px;
  color: #374151;
}

input, select {
  padding: 10px;
  border: 1px solid #e5e7eb;
  border-radius: 10px;
  outline: none;
  transition: 0.2s;
  background: white;
  font-size: 14px;
  color: #111827;
}

input:focus, select:focus {
  border-color: #6366f1;
  box-shadow: 0 0 0 3px rgba(99,102,241,0.15);
}

.toggle {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  user-select: none;
}

.toggle input {
  width: 18px;
  height: 18px;
}

.btn {
  margin-top: 20px;
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: 10px;
  background: #4f46e5;
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: 0.2s;
}

.btn:hover {
  background: #4338ca;
}

.results {
  margin-top: 20px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.result-card {
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.result-card.full {
  grid-column: 1 / -1;
}

.result-label {
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: #94a3b8;
}

.result-value {
  font-size: 28px;
  font-weight: 700;
  color: #0f172a;
}

.result-desc {
  font-size: 14px;
  color: #374151;
  line-height: 1.5;
}

.capitalize {
  text-transform: capitalize;
}

/* Risk level tinting */
.result-card.risk.low    { border-color: #86efac; background: #f0fdf4; }
.result-card.risk.low    .result-value { color: #16a34a; }

.result-card.risk.moderate { border-color: #fde68a; background: #fffbeb; }
.result-card.risk.moderate .result-value { color: #d97706; }

.result-card.risk.high   { border-color: #fca5a5; background: #fef2f2; }
.result-card.risk.high   .result-value { color: #dc2626; }

@media (max-width: 640px) {
  .grid {
    grid-template-columns: 1fr;
  }
}
</style>
