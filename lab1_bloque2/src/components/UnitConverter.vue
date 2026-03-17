<script setup>
import { ref, computed, watch } from 'vue'

// ── Categorías y unidades ─────────────────────────────────────────────
const categories = {
  longitud: {
    label: '📏 Longitud',
    units: {
      km:       { label: 'Kilómetro (km)',    factor: 1000 },
      m:        { label: 'Metro (m)',          factor: 1 },
      cm:       { label: 'Centímetro (cm)',    factor: 0.01 },
      mm:       { label: 'Milímetro (mm)',     factor: 0.001 },
      pies:     { label: 'Pies (ft)',          factor: 0.3048 },
      pulgadas: { label: 'Pulgadas (in)',      factor: 0.0254 },
    }
  },
  peso: {
    label: '⚖️ Peso',
    units: {
      kg:  { label: 'Kilogramo (kg)', factor: 1 },
      g:   { label: 'Gramo (g)',      factor: 0.001 },
      mg:  { label: 'Miligramo (mg)', factor: 0.000001 },
      lb:  { label: 'Libra (lb)',     factor: 0.453592 },
      oz:  { label: 'Onza (oz)',      factor: 0.0283495 },
    }
  },
  temperatura: {
    label: '🌡️ Temperatura',
    units: {
      celsius:    { label: 'Celsius (°C)' },
      fahrenheit: { label: 'Fahrenheit (°F)' },
      kelvin:     { label: 'Kelvin (K)' },
    }
  }
}

// ── Variables reactivas ───────────────────────────────────────────────
const activeCategory = ref('longitud')  // 1
const fromUnit       = ref('km')        // 2
const toUnit         = ref('m')         // 3
const inputValue     = ref('')          // 4
const result         = ref(null)        // 5
const errorMsg       = ref('')          // 6 
const historial      = ref([])          // 7 

const currentUnits = computed(() => categories[activeCategory.value].units)

watch(activeCategory, () => {
  const keys = Object.keys(currentUnits.value)
  fromUnit.value   = keys[0]
  toUnit.value     = keys[1]
  inputValue.value = ''
  result.value     = null
  errorMsg.value   = ''
})

// ── Conversión ────────────────────────────────────────────────────────
function convert() {
  errorMsg.value = ''
  const val = parseFloat(inputValue.value)

  if (inputValue.value === '' || isNaN(val)) {
    errorMsg.value = 'Por favor ingresa un valor numérico válido.'
    result.value = null
    return
  }

  if (activeCategory.value === 'temperatura') {
    result.value = convertTemp(val, fromUnit.value, toUnit.value)
  } else {
    const base = val * currentUnits.value[fromUnit.value].factor
    result.value = base / currentUnits.value[toUnit.value].factor
  }

  // Guardar en historial
  const fromLabel = currentUnits.value[fromUnit.value].label.split(' ')[0]
  const toLabel   = currentUnits.value[toUnit.value].label.split(' ')[0]
  historial.value.unshift({
    from: `${val} ${fromLabel}`,
    to:   `${formatResult(result.value)} ${toLabel}`,
    cat:  categories[activeCategory.value].label
  })
  if (historial.value.length > 6) historial.value.pop()
}

function convertTemp(val, from, to) {
  let celsius
  if (from === 'celsius')    celsius = val
  if (from === 'fahrenheit') celsius = (val - 32) * 5 / 9
  if (from === 'kelvin')     celsius = val - 273.15
  if (to === 'celsius')    return celsius
  if (to === 'fahrenheit') return celsius * 9 / 5 + 32
  if (to === 'kelvin')     return celsius + 273.15
}

function swap() {
  const tmp      = fromUnit.value
  fromUnit.value = toUnit.value
  toUnit.value   = tmp
  result.value   = null
}

function limpiarHistorial() {
  historial.value = []
}

function formatResult(n) {
  if (n === null) return ''
  if (n === 0) return '0'
  return parseFloat(n.toFixed(2)).toString() 
}

const displayResult = computed(() =>
  result.value !== null ? formatResult(result.value) : '—'
)

const resultUnit = computed(() =>
  currentUnits.value[toUnit.value]?.label.split(' ')[0] ?? ''
)
</script>

<template>
  <div class="converter">
    <h2 class="title">Conversor</h2>

    <!--  v-for: itera categorías para generar tabs -->
    <!--  v-bind: enlaza la clase activa dinámicamente -->
    <div class="tabs">
      <button
        v-for="(cat, key) in categories"
        :key="key"
        v-bind:class="{ active: activeCategory === key }"
        @click="activeCategory = key"
      >
        {{ cat.label }}
      </button>
    </div>

    <!-- v-if: solo muestra el error si existe errorMsg -->
    <div v-if="errorMsg" class="error-box">
       {{ errorMsg }}
    </div>

    <div class="form">
      <!--  v-model: enlaza el valor del input -->
      <!--  v-bind: deshabilita el botón si el input está vacío -->
      <input
        v-model="inputValue"
        type="number"
        placeholder="Ingresa un valor"
      />

      <div class="selects">
        <!--  v-model: enlaza la unidad origen -->
        <!--  v-for: genera las opciones del select -->
        <select v-model="fromUnit">
          <option
            v-for="(unit, key) in currentUnits"
            :key="key"
            v-bind:value="key"
          >
            {{ unit.label }}
          </option>
        </select>

        <button class="swap-btn" @click="swap" title="Intercambiar">⇄</button>

        <!--  v-model: enlaza la unidad destino -->
        <!-- v-for + v-bind:value -->
        <select v-model="toUnit">
          <option
            v-for="(unit, key) in currentUnits"
            :key="key"
            v-bind:value="key"
          >
            {{ unit.label }}
          </option>
        </select>
      </div>

      <!--  v-bind:disabled desactiva el botón si no hay valor -->
      <button
        class="convert-btn"
        v-bind:disabled="inputValue === ''"
        @click="convert"
      >
        Convertir
      </button>
    </div>

    <!-- Resultado -->
    <div class="result-box">
      <span class="result-value">{{ displayResult }}</span>
      <span class="result-unit">{{ result !== null ? resultUnit : '' }}</span>
    </div>

    <!-- Historial -->
    <div class="hist-header">
      <span class="hist-label">HISTORIAL</span>
      <button class="clear-btn" @click="limpiarHistorial">Limpiar</button>
    </div>

    <!--  v-if / v-else: muestra lista o mensaje vacío -->
    <div v-if="historial.length > 0" class="hist-list">
      <!--  v-for: itera las entradas del historial -->
      <div
        v-for="(entry, index) in historial"
        :key="index"
        class="hist-item"
      >
        <span class="hist-cat">{{ entry.cat }}</span>
        <span class="hist-conv">{{ entry.from }} → {{ entry.to }}</span>
      </div>
    </div>
    <div v-else class="hist-empty">Sin conversiones aún</div>

  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=DM+Sans:wght@300;400;500&display=swap');

.converter {
  width: 320px;
  border-radius: 28px;
  background: #18181d;
  box-shadow: 0 30px 80px rgba(0,0,0,.7), 0 0 0 1px #2a2a33;
  padding: 1.6rem 1.4rem;
  font-family: 'DM Sans', sans-serif;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.title {
  font-family: 'Syne', sans-serif;
  font-size: 1.6rem;
  font-weight: 800;
  color: #f0f0f0;
  letter-spacing: -0.5px;
  text-align: center;
}

/* ── Error ─── */
.error-box {
  background: #3a1515;
  border: 1.5px solid #c0392b;
  border-radius: 12px;
  padding: .5rem .8rem;
  font-size: .82rem;
  color: #ff6b6b;
}

/* ── Tabs ─── */
.tabs {
  display: flex;
  gap: .4rem;
  flex-wrap: wrap;
  justify-content: center;
}
.tabs button {
  background: #2a2a33;
  border: 1.5px solid #2a2a33;
  color: #7a7a8a;
  border-radius: 10px;
  padding: .35rem .7rem;
  font-family: 'DM Sans', sans-serif;
  font-size: .78rem;
  cursor: pointer;
  transition: all .15s;
  white-space: nowrap;
}
.tabs button:hover  { border-color: #3a3a46; color: #f0f0f0; }
.tabs button.active { background: #c8f058; border-color: #c8f058; color: #111; font-weight: 500; }

/* ── Form ─── */
.form { display: flex; flex-direction: column; gap: .7rem; }

input[type="number"] {
  width: 100%;
  background: #111116;
  border: 1.5px solid #2a2a33;
  border-radius: 14px;
  padding: .8rem 1rem;
  color: #f0f0f0;
  font-family: 'Syne', sans-serif;
  font-size: 1.4rem;
  font-weight: 700;
  outline: none;
  transition: border-color .2s;
  -moz-appearance: textfield;
  box-sizing: border-box;
}
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button { -webkit-appearance: none; }
input:focus { border-color: #c8f058; }
input::placeholder { color: #3a3a46; font-size: 1rem; font-weight: 300; }

/* ── Botón Convertir ─── */
.convert-btn {
  background: #c8f058;
  border: none;
  border-radius: 12px;
  padding: .7rem;
  color: #111;
  font-family: 'DM Sans', sans-serif;
  font-size: .9rem;
  font-weight: 500;
  cursor: pointer;
  transition: opacity .2s;
}
.convert-btn:disabled { opacity: 0.4; cursor: not-allowed; }
.convert-btn:not(:disabled):hover { opacity: 0.85; }

/* ── Selects row ─── */
.selects { display: flex; align-items: center; gap: .5rem; }

select {
  flex: 1;
  background: #1f1f26;
  border: 1.5px solid #2a2a33;
  border-radius: 12px;
  padding: .55rem .7rem;
  color: #f0f0f0;
  font-family: 'DM Sans', sans-serif;
  font-size: .82rem;
  outline: none;
  cursor: pointer;
  transition: border-color .2s;
  appearance: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 24 24' fill='none' stroke='%237a7a8a' stroke-width='2.5'%3E%3Cpolyline points='6 9 12 15 18 9'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right .6rem center;
  padding-right: 1.8rem;
}
select:focus { border-color: #c8f058; }
select option { background: #1f1f26; }

.swap-btn {
  flex-shrink: 0;
  background: #2a2a33;
  border: 1.5px solid #2a2a33;
  color: #c8f058;
  border-radius: 10px;
  width: 36px; height: 36px;
  font-size: 1.1rem;
  cursor: pointer;
  transition: background .15s, transform .2s;
  display: flex; align-items: center; justify-content: center;
  padding: 0;
}
.swap-btn:hover { background: #333340; transform: rotate(180deg); }

/* ── Resultado ─── */
.result-box {
  background: #111116;
  border: 1.5px solid #2a2a33;
  border-radius: 14px;
  padding: 1rem 1.2rem;
  display: flex;
  align-items: baseline;
  justify-content: flex-end;
  gap: .5rem;
  min-height: 64px;
}
.result-value {
  font-family: 'Syne', sans-serif;
  font-size: 1.9rem;
  font-weight: 800;
  color: #c8f058;
  letter-spacing: -1px;
  word-break: break-all;
  text-align: right;
}
.result-unit { font-size: .82rem; color: #7a7a8a; white-space: nowrap; }

/* ── Historial ─── */
.hist-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.hist-label { font-size: .78rem; color: #7a7a8a; font-weight: 500; letter-spacing: .05em; }
.clear-btn {
  background: transparent; border: none; color: #7a7a8a;
  font-size: .75rem; cursor: pointer;
}
.clear-btn:hover { color: #f0f0f0; }

.hist-list { display: flex; flex-direction: column; gap: .4rem; max-height: 140px; overflow-y: auto; }

.hist-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #1f1f26;
  border-radius: 8px;
  padding: .35rem .7rem;
}
.hist-cat  { font-size: .72rem; color: #7a7a8a; }
.hist-conv { font-size: .78rem; color: #f0f0f0; }

.hist-empty { text-align: center; font-size: .78rem; color: #3a3a46; padding: .5rem 0; }
</style>
