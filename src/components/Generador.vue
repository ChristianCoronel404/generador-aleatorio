<template>
  <div class="app-container">
    <!-- Barra superior -->
    <header class="top-bar">
      <h1>Generador Congruencial de Números Aleatorios</h1>
    </header>

    <!-- Tabs -->
    <div class="tabs">
      <button :class="{active: activeTab==='lineal'}" @click="activeTab='lineal'">Lineal</button>
      <button :class="{active: activeTab==='multiplicativo'}" @click="activeTab='multiplicativo'">Multiplicativo</button>
    </div>

    <!-- Contenido Lineal -->
    <div v-if="activeTab==='lineal'" class="generator">
      <h2>Lineal</h2>
      <div class="inputs">
        <label>Semilla (X₀): <input v-model.number="x0Lineal" type="number" /></label>
        <label>k (a = 1 + 4k): <input v-model.number="kLineal" type="number" /></label>
        <label>c (primo relativo con m): <input v-model.number="cLineal" type="number" /></label>
        <label>p (m = p, cantidad de números): <input v-model.number="pLineal" type="number" /></label>
      </div>
      <button @click="congruencialLineal">Generar Lineal</button>

      <!-- Recuadro parámetros -->
      <div v-if="paramsLineal" class="params-box">
        <h3>Parámetros calculados</h3>
        <p><b>a:</b> 1 + 4·{{ kLineal }} = {{ paramsLineal.a }}</p>
        <p><b>c:</b> {{ cLineal }}</p>
        <p><b>m:</b> p = {{ paramsLineal.m }}</p>
        <p><b>g:</b> log₂({{ paramsLineal.m }}) = {{ paramsLineal.g }}</p>
      </div>

      <!-- Tabla resultados -->
      <table v-if="tablaLineal.length" class="result-table">
        <thead>
          <tr>
            <th>#</th>
            <th>Xi-1</th>
            <th>Operación</th>
            <th>Xi</th>
            <th>Ri</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row,i) in tablaLineal" :key="i" :class="{extra:i===pLineal}">
            <td>{{ i+1 }}</td>
            <td>{{ row.prev }}</td>
            <td>{{ row.op }}</td>
            <td>{{ row.xi }}</td>
            <td>{{ row.ri }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Contenido Multiplicativo -->
    <div v-if="activeTab==='multiplicativo'" class="generator">
      <h2>Multiplicativo</h2>
      <div class="inputs">
        <label>Semilla (X₀): <input v-model.number="x0Mult" type="number" /></label>

        <!-- Selector para que el usuario elija la forma de 'a' -->
        <label>
          Fórmula para a:
          <select v-model="aOption">
            <option value="3">3 + 8k</option>
            <option value="5">5 + 8k</option>
          </select>
        </label>

        <label>k (para a): <input v-model.number="kMult" type="number" /></label>
        <label>P: <input v-model.number="pMult" type="number" /></label>
      </div>
      <button @click="congruencialMultiplicativo">Generar Multiplicativo</button>

      <!-- Recuadro parámetros -->
      <div v-if="paramsMult" class="params-box">
        <h3>Parámetros calculados</h3>
        <!-- mostramos la fórmula exactamente con el 3 o 5 elegido -->
        <p><b>a:</b> {{ baseA }} + 8·{{ kMult }} = {{ paramsMult.a }}</p>
        <p><b>m:</b> 2^{{ paramsMult.g }} = {{ paramsMult.m }}</p>
        <p><b>g:</b> {{ paramsMult.g }}</p>
      </div>

      <!-- Tabla resultados (multiplicativo) -->
      <table v-if="tablaMult.length" class="result-table">
        <thead>
          <tr>
            <th>#</th>
            <th>Xi-1</th>
            <th>Operación</th>
            <th>Xi</th>
            <th>Ri</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row,i) in tablaMult" :key="i" :class="{extra:i===tablaMult.length-1}">
            <td>{{ i+1 }}</td>
            <td>{{ row.prev }}</td>
            <td>{{ row.op }}</td>
            <td>{{ row.xi }}</td>
            <td>{{ row.ri }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const activeTab = ref('lineal')

// Lineal
const x0Lineal = ref(1)
const kLineal = ref(1)
const cLineal = ref(3)
const pLineal = ref(8)
const tablaLineal = ref([])
const paramsLineal = ref(null)

// Multiplicativo
const x0Mult = ref(1)
const kMult = ref(0)
const pMult = ref(3) // g (mantengo tu nombre)
const tablaMult = ref([])
const paramsMult = ref(null)

// opción para elegir 3+8k o 5+8k; guardamos '3' o '5'
const aOption = ref('3')

// para mostrar fácilmente el número base en la plantilla
const baseA = computed(() => aOption.value)

// Función auxiliar (no tocada)
function esPrimoRelativo(a, b) {
  while (b !== 0) {
    let temp = b
    b = a % b
    a = temp
  }
  return a === 1
}

// Lineal: sin cambios en fórmulas
function congruencialLineal() {
  const m = pLineal.value
  const g = Math.log(m) / Math.log(2)
  const a = 1 + 4 * kLineal.value
  if (!Number.isInteger(g)) {
    alert("p debe ser potencia de 2")
    return
  }
  if (!esPrimoRelativo(cLineal.value, m)) {
    alert("c debe ser primo relativo con m")
    return
  }

  paramsLineal.value = {a, m, g}
  let x = x0Lineal.value
  let tabla = []
  for (let i = 0; i <= m; i++) {
    let prev = x
    x = (a * x + cLineal.value) % m
    let ri = (x / (m-1)).toFixed(4)
    tabla.push({
      prev: prev,
      op: `(${a}*${prev} + ${cLineal.value}) mod ${m}`,
      xi: x,
      ri
    })
  }
  tablaLineal.value = tabla
}

// Multiplicativo: mantengo el resto de las fórmulas exactamente como estaban,
// sólo permito elegir base (3 o 5) para la expresión de 'a'.
function congruencialMultiplicativo() {
  if (x0Mult.value % 2 === 0) {
    alert("x0 debe ser impar")
    return
  }

  // Validar que pMult sea potencia de 2
  const g = Math.log2(pMult.value)
  if (!Number.isInteger(g)) {
    alert("P debe ser potencia de 2")
    return
  }
  const m = 2 ** (g + 2) // m = 2^(g+2), como en tu lógica original

  const base = parseInt(aOption.value, 10) // 3 o 5
  const a = base + 8 * kMult.value

  paramsMult.value = {a, m, g: g + 2}

  let x = x0Mult.value
  let tabla = []
  const seen = new Set()
  for (let i = 0; i < pMult.value; i++) {
    let prev = x
    x = (a * x) % m
    let ri = (x / (m - 1)).toFixed(4)
    tabla.push({
      prev,
      op: `(${a}*${prev}) mod ${m}`,
      xi: x,
      ri
    })
    if (seen.has(x)) break
    seen.add(x)
  }
  tablaMult.value = tabla
}
</script>

<style scoped>
.top-bar {background:#4a90e2;color:#fff;padding:20px;text-align:center;}
.tabs {display:flex;gap:10px;justify-content:center;margin:20px;}
.tabs button {padding:10px 20px;border:none;cursor:pointer;border-radius:6px;background:#eee;font-weight:bold;}
.tabs button.active {background:#4a90e2;color:white;}
.generator {max-width:700px;margin:0 auto;background:#f4f4f4;padding:20px;border-radius:10px;}
.inputs label {display:block;margin:8px 0;}
input, select {padding:5px;width:100%;margin-top:5px;}
button{margin-top:10px;padding:10px 20px;background:#4a90e2;color:#fff;border:none;border-radius:5px;}
button:hover{background:#357abd;}
.params-box{background:white;padding:10px;border:1px solid #ccc;margin-top:10px;}
.params-box h3{margin-bottom:8px;}
.result-table{width:100%;margin-top:15px;border-collapse:collapse;}
.result-table th,.result-table td{border:1px solid #ccc;padding:6px;text-align:center;}
.result-table tr.extra{background:#ffe0e0;}
</style>
