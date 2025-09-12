<template>
  <div class="app-container">
    <!-- Barra superior -->
    <header class="top-bar">
      <h1>Generador Congruencial de Números Aleatorios</h1>
    </header>

    <div class="generators">
      <!-- Lineal -->
      <div class="generator">
        <h2>Lineal</h2>
        <div class="inputs">
          <label>Semilla (X₀): <input v-model.number="x0Lineal" type="number" /></label>
          <label>k (para a = 1 + 4k): <input v-model.number="kLineal" type="number" /></label>
          <label>c (relativamente primo con m): <input v-model.number="cLineal" type="number" /></label>
          <label>p (m = p y cantidad de números): <input v-model.number="pLineal" type="number" /></label>
        </div>
        <button @click="congruencialLineal">Generar Lineal</button>

        <h3>Resultados (Xi):</h3>
        <pre>{{ enterosLineal.join(', ') }}</pre>
        <h3>Resultados normalizados (ri):</h3>
        <pre>{{ secuenciaLineal.join(', ') }}</pre>
      </div>

      <!-- Multiplicativo -->
      <div class="generator">
        <h2>Multiplicativo</h2>
        <div class="inputs">
          <label>Semilla (X₀): <input v-model.number="x0Mult" type="number" /></label>
          <label>k (para a = 3 + 8k): <input v-model.number="kMult" type="number" /></label>
          <label>p (m = 2^g y cantidad de números): <input v-model.number="pMult" type="number" /></label>
        </div>
        <button @click="congruencialMultiplicativo">Generar Multiplicativo</button>

        <h3>Resultados (Xi):</h3>
        <pre>{{ enterosMult.join(', ') }}</pre>
        <h3>Resultados normalizados (ri):</h3>
        <pre>{{ secuenciaMult.join(', ') }}</pre>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// Variables Lineal
const x0Lineal = ref(1)
const kLineal = ref(1)
const cLineal = ref(3)
const pLineal = ref(8)
const enterosLineal = ref([])
const secuenciaLineal = ref([])

// Variables Multiplicativo
const x0Mult = ref(1)
const kMult = ref(0)
const pMult = ref(3) // g
const enterosMult = ref([])
const secuenciaMult = ref([])

// Función para verificar números relativamente primos
function esPrimoRelativo(a, b) {
  while (b !== 0) {
    let temp = b
    b = a % b
    a = temp
  }
  return a === 1
}

// Método Lineal
function congruencialLineal() {
  const m = pLineal.value
  const g = Math.log(pLineal.value) / Math.log(2)
  const a = 1 + 4 * kLineal.value

  if (!Number.isInteger(g)) {
    alert("p debe ser una potencia de 2")
    return
  }
  if (!esPrimoRelativo(cLineal.value, m)) {
    alert("c debe ser relativamente primo con m")
    return
  }

  let x = x0Lineal.value
  let xi = []
  let ri = []

  for (let i = 0; i < m; i++) {
    x = (a * x + cLineal.value) % m
    xi.push(x)
    ri.push((x / (m - 1)).toFixed(4))
  }

  enterosLineal.value = xi
  secuenciaLineal.value = ri
}

// Método Multiplicativo
function congruencialMultiplicativo() {
  if (x0Mult.value % 2 === 0) {
    alert("x0 debe ser impar para máximo periodo")
    return
  }

  const g = (Math.log(pMult.value) / Math.log(2))+2
  if (!Number.isInteger(g)) {
    alert("g debe ser un entero")
    return
  }

  const m = 2 ** g
  const a = 5 + 8 * kMult.value
  let x = x0Mult.value
  let xi = []
  let ri = []

  for (let i = 0; i < m; i++) {
    x = (a * x) % m
    xi.push(x)
    ri.push((x / (m - 1)).toFixed(4))
  }

  enterosMult.value = xi
  secuenciaMult.value = ri
}
</script>

<style scoped>
/* Barra superior */
.top-bar {
  background-color: #4a90e2;
  color: white;
  padding: 20px;
  text-align: center;
  font-family: 'Segoe UI', sans-serif;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

/* Contenedor de generadores */
.generators {
  display: flex;
  justify-content: space-between;
  margin: 20px;
  gap: 20px;
  font-family: 'Segoe UI', sans-serif;
}

/* Cada generador */
.generator {
  flex: 1;
  background: #f4f4f4;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

/* Inputs y etiquetas */
.inputs label {
  display: block;
  margin-bottom: 10px;
}

input {
  padding: 5px;
  width: 100%;
  border-radius: 4px;
  border: 1px solid #ccc;
}

/* Botones */
button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #4a90e2;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
}

button:hover {
  background-color: #357abd;
}

/* Resultados */
pre {
  background: white;
  padding: 10px;
  border-radius: 6px;
  max-height: 150px;
  overflow-y: auto;
}
</style>
