<template>
  <div class="app-container">
    <header class="top-bar">
      <h1>Generador Congruencial de Números Aleatorios</h1>
      <p>Explora y genera secuencias de números pseudoaleatorios</p>
    </header>

    <div class="tabs-container">
      <div class="tabs">
        <button
          :class="{ active: activeTab === 'lineal' }"
          @click="activeTab = 'lineal'"
        >
          Lineal
        </button>
        <button
          :class="{ active: activeTab === 'multiplicativo' }"
          @click="activeTab = 'multiplicativo'"
        >
          Multiplicativo
        </button>
      </div>
    </div>

    <main class="content-wrapper">
      <div class="control-panel">
        <!-- Generador Lineal -->
        <div v-if="activeTab === 'lineal'" class="card animate-fade-in">
          <h2>Generador Lineal</h2>
          <div class="inputs-grid">
            <div class="input-group">
              <label for="x0Lineal">Semilla (X₀):</label>
              <input
                id="x0Lineal"
                v-model.number="x0Lineal"
                type="number"
                placeholder="Ej: 1"
              />
            </div>
            <div class="input-group">
              <label for="kLineal">k (Entero positivo):</label>
              <input
                id="kLineal"
                v-model.number="kLineal"
                type="number"
                placeholder="Ej: 1"
              />
            </div>
            <div class="input-group">
              <label for="cLineal">c (Incremento):</label>
              <input
                id="cLineal"
                v-model.number="cLineal"
                type="number"
                placeholder="Ej: 3"
              />
            </div>
            <div class="input-group">
              <label for="pLineal">p (Máximo Periodo):</label>
              <input
                id="pLineal"
                v-model.number="pLineal"
                type="number"
                placeholder="Ej: 8"
              />
            </div>
            <div class="input-group">
              <label for="decimalesLineal">Dígitos para redondear R:</label>
              <input
                id="decimalesLineal"
                v-model.number="decimalesLineal"
                type="number"
                placeholder="Ej: 4"
              />
            </div>
          </div>
          <button @click="congruencialLineal">Generar Números</button>

          <!-- Cuadro de parámetros calculados -->
          <div v-if="paramsLineal" class="params-box animate-fade-in mt-4">
            <h3>Parámetros calculados</h3>
            <p>
              <b>a:</b> 1 + 4k <br />
               a = 1 + 4 * {{ kLineal }} = {{ paramsLineal.a }}
            </p>
            <p>
              <b>m:</b> p = 2^g<br />
               m = p = 2^{{ paramsLineal.g }} = {{ paramsLineal.m }}
            </p>
            <p>
              <b>g:</b> log₂(p) <br />
               g = log₂({{ pLineal }}) = {{ paramsLineal.g }}
            </p>
          </div>
        </div>

        <!-- Generador Multiplicativo -->
<div v-if="activeTab === 'multiplicativo'" class="card animate-fade-in">
  <h2>Generador Multiplicativo</h2>
  <div class="inputs-grid">
    <div class="input-group">
      <label for="x0Mult">Semilla (X₀):</label>
      <input
        id="x0Mult"
        v-model.number="x0Mult"
        type="number"
        placeholder="Ej: 1"
      />
    </div>
    <div class="input-group">
      <label for="kMult">k (Entero positivo):</label>
      <input
        id="kMult"
        v-model.number="kMult"
        type="number"
        placeholder="Ej: 0"
      />
    </div>
    <div class="input-group">
      <label for="pMult">p (Periodo):</label>
      <input
        id="pMult"
        v-model.number="pMult"
        type="number"
        placeholder="Ej: 3"
      />
    </div>
    <div class="input-group">
      <label for="decimalesMult">Dígitos para redondear R:</label>
      <input
        id="decimalesMult"
        v-model.number="decimalesMult"
        type="number"
        placeholder="Ej: 4"
      />
    </div>
    <div class="radio-group-container">
      <label>Fórmula para 'a':</label>
      <div class="radio-group">
        <div class="radio-option">
          <input type="radio" id="aOption3" value="3" v-model="aOption" />
          <label for="aOption3">3 + 8k</label>
        </div>
        <div class="radio-option">
          <input type="radio" id="aOption5" value="5" v-model="aOption" />
          <label for="aOption5">5 + 8k</label>
        </div>
      </div>
    </div>
  </div>
  <button @click="congruencialMultiplicativo">Generar Números</button>

  <!-- Cuadro de parámetros calculados -->
  <div v-if="paramsMult" class="params-box animate-fade-in mt-4">
    <h3>Parámetros calculados</h3>
    <p>
      <b>a:</b> base (3 ó 5) + 8k <br />
      a = {{ aOption }} + 8 * {{ kMult }} = {{ paramsMult.a }}
    </p>
    <p>
      <b>m:</b> 2^g <br />
      m = 2^{{ paramsMult.g }} = {{ paramsMult.m }}
    </p>
    <p>
      <b>g:</b> log₂(p)+2 <br />
      g = log₂({{ pMult }}) + 2 = {{ paramsMult.g }}
    </p>
  </div>
</div>

      </div>

      <!-- Panel de resultados Lineal -->
      <div v-if="activeTab === 'lineal'" class="result-panel">
        <div v-if="tablaLineal.length" class="table-container animate-fade-in">
          <h3>Tabla de Resultados (Lineal)</h3>
          <table class="result-table">
            <thead>
              <tr>
                <th>#</th>
                <th>Xᵢ₋₁</th>
                <th>Operación</th>
                <th>Xᵢ</th>
                <th>Rᵢ</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, i) in tablaLineal" :key="i" :class="{ extra: i === pLineal }">
                <td>{{ i + 1 }}</td>
                <td>{{ row.prev }}</td>
                <td>{{ row.op }}</td>
                <td>{{ row.xi }}</td>
                <td>{{ row.ri }}</td>
              </tr>
              <!-- Fila adicional para verificar el periodo -->
              <tr class="table-warning">
                <td>{{ tablaLineal.length + 1 }}</td>
                <td>{{ tablaLineal[0].prev }}</td>
                <td>{{ tablaLineal[0].op }}</td>
                <td>{{ tablaLineal[0].xi }}</td>
                <td>{{ tablaLineal[0].ri }}</td>
              </tr>
            </tbody>
          </table>
          <!-- Aclaración sobre el inicio del nuevo periodo -->
          <p class="text-warning mt-3">
            <b>Nota:</b> La última fila indica el inicio de otro periodo. A partir de este punto, los valores generados se repetirán siguiendo el mismo patrón.
          </p>
        </div>
      </div>

      <!-- Panel de resultados Multiplicativo -->
      <div v-if="activeTab === 'multiplicativo'" class="result-panel">
        <div v-if="tablaMult.length" class="table-container animate-fade-in">
          <h3>Tabla de Resultados (Multiplicativo)</h3>
          <table class="result-table">
            <thead>
              <tr>
                <th>#</th>
                <th>Xᵢ₋₁</th>
                <th>Operación</th>
                <th>Xᵢ</th>
                <th>Rᵢ</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, i) in tablaMult" :key="i">
                <td>{{ i + 1 }}</td>
                <td>{{ row.prev }}</td>
                <td>{{ row.op }}</td>
                <td>{{ row.xi }}</td>
                <td>{{ row.ri }}</td>
              </tr>
              <!-- Fila adicional para verificar el periodo -->
              <tr class="table-warning">
                <td>{{ tablaMult.length + 1 }}</td>
                <td>{{ tablaMult[0].prev }}</td>
                <td>{{ tablaMult[0].op }}</td>
                <td>{{ tablaMult[0].xi }}</td>
                <td>{{ tablaMult[0].ri }}</td>
              </tr>
            </tbody>
          </table>
          <!-- Aclaración sobre el inicio del nuevo periodo -->
          <p class="text-warning mt-3">
            <b>Nota:</b> La última fila indica el inicio de otro periodo. A partir de este punto, los valores generados se repetirán siguiendo el mismo patrón.
          </p>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const activeTab = ref('lineal');
const x0Lineal = ref(1);
const kLineal = ref(1);
const cLineal = ref(3);
const pLineal = ref(8);
const decimalesLineal = ref(4);
const tablaLineal = ref([]);
const paramsLineal = ref(null);

const x0Mult = ref(1);
const kMult = ref(5);
const pMult = ref(4);
const decimalesMult = ref(4);
const tablaMult = ref([]);
const paramsMult = ref(null);
const aOption = ref('3');

function esPrimoRelativo(a, b) {
  while (b !== 0) {
    let temp = b;
    b = a % b;
    a = temp;
  }
  return a === 1;
}

function congruencialLineal() {
  if (kLineal.value <= 0 || !Number.isInteger(kLineal.value)) {
    alert("k debe ser un entero positivo.");
    return;
  }
  if (decimalesLineal.value <= 0 || !Number.isInteger(decimalesLineal.value)) {
    alert("La cantidad de dígitos para redondear debe ser un entero positivo.");
    return;
  }
  const m = pLineal.value;
  const gRaw = Math.log2(m);
  const g = Math.floor(gRaw);
  const a = 1 + 4 * kLineal.value;

  if (Math.abs(gRaw - g) > 1e-9) {
    alert("p debe ser una potencia de 2.");
    return;
  }
  if (!esPrimoRelativo(cLineal.value, m)) {
    alert("c debe ser primo relativo con m.");
    return;
  }

  paramsLineal.value = { a, m, g };
  let x = x0Lineal.value;
  let tabla = [];
  for (let i = 0; i < m; i++) {
    const prev = x;
    x = (a * x + cLineal.value) % m;
    // const ri = parseFloat((x / (m - 1)).toFixed(decimalesLineal.value));
    const ri = (x / (m - 1)).toFixed(decimalesLineal.value);
    tabla.push({ prev, op: `(${a}*${prev} + ${cLineal.value}) mod ${m}`, xi: x, ri, div: `${x} / (${m}-1) = ${ri}` });
  }
  tablaLineal.value = tabla;
}

function congruencialMultiplicativo() {
  if (kMult.value <= 0 || !Number.isInteger(kMult.value)) {
    alert("k debe ser un entero positivo.");
    return;
  }
  if (decimalesMult.value <= 0 || !Number.isInteger(decimalesMult.value)) {
    alert("La cantidad de dígitos para redondear debe ser un entero positivo.");
    return;
  }
  const p = pMult.value;
  const gRaw = Math.log2(p);
  const g = Math.floor(gRaw);

  if (Math.abs(gRaw - g) > 1e-9) {
    alert("p debe ser una potencia de 2.");
    return;
  }

  const m = 2 ** (g + 2);
  const base = parseInt(aOption.value, 10);
  const a = base + 8 * kMult.value;

  if (x0Mult.value % 2 === 0) {
    alert("X₀ debe ser impar.");
    return;
  }

  paramsMult.value = { a, m, g: g + 2 };
  let x = x0Mult.value;
  let tabla = [];
  const seen = new Set();
  for (let i = 0; i < p; i++) {
    const prev = x;
    x = (a * x) % m;
    // const ri = parseFloat((x / (m - 1)).toFixed(decimalesMult.value));
    const ri = (x / (m - 1)).toFixed(decimalesMult.value);
    tabla.push({ prev, op: `(${a}*${prev}) mod ${m}`, xi: x, ri, div: `${x} / (${m}-1) = ${ri}` });
    if (seen.has(x)) break;
    seen.add(x);
  }
  tablaMult.value = tabla;
}
</script>


<style>
/* --- PALETA DE COLORES Y TIPOGRAFÍA ---
  Se han definido colores y fuentes para una apariencia profesional.
*/
:root {
  --primary-color: #2c3e50; /* Azul muy oscuro para el fondo del header y texto principal */
  --secondary-color: #3498db; /* Azul vibrante para botones, títulos y elementos interactivos */
  --bg-color: #ecf0f1; /* Gris claro para el fondo principal */
  --card-bg-color: #ffffff; /* Blanco puro para el fondo de las tarjetas */
  --text-dark: #2c3e50; /* Texto oscuro para una mejor lectura */
  --text-light: #ffffff; /* Texto claro en fondos oscuros */
  --border-color: #bdc3c7; /* Gris suave para bordes y líneas divisorias */
  --accent-color: #e74c3c; /* Rojo para estados de alerta o errores */
  --success-color: #2ecc71; /* Verde para éxito o información relevante */
  --shadow-light: 0 4px 15px rgba(0, 0, 0, 0.08); /* Sombra suave para elevación */
  --shadow-card: 0 8px 30px rgba(0, 0, 0, 0.1); /* Sombra más pronunciada para tarjetas */
  --font-family-main: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/*
  --- ESTILOS GENERALES Y MAQUETACIÓN ---
  Se ha simplificado la estructura para una sola división.
*/
html, body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: var(--font-family-main);
  background-color: var(--bg-color);
  color: var(--text-dark);
}

.app-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  width: 100%;
}

.top-bar {
  background: var(--primary-color);
  color: var(--text-light);
  padding: 3rem 1rem;
  text-align: center;
  box-shadow: var(--shadow-light);
}

.top-bar h1 {
  margin: 0;
  font-size: clamp(1.8rem, 5vw, 2.8rem);
  font-weight: 700;
  letter-spacing: 1px;
}

.top-bar p {
  margin-top: 0.5rem;
  font-size: clamp(0.8rem, 2vw, 1.1rem);
  opacity: 0.8;
}

.tabs-container {
  background: var(--bg-color);
  padding: 1rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  display: flex;
  justify-content: center;
  border-bottom: 2px solid var(--border-color);
}

.tabs {
  display: flex;
  width: 100%;
  max-width: 900px;
  gap: 1.5rem;
}

.tabs button {
  flex-grow: 1;
  padding: 0.8rem 2.5rem;
  border: none;
  border-radius: 999px;
  background: var(--border-color);
  color: var(--primary-color);
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
}

.tabs button:hover {
  background: var(--secondary-color);
  color: var(--text-light);
  transform: translateY(-3px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
}

.tabs button.active {
  background: var(--secondary-color);
  color: var(--text-light);
  transform: translateY(-1px);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

/*
  --- NUEVA ESTRUCTURA DE CONTENIDO ---
  Diseño de un solo panel dividido verticalmente.
*/
.content-wrapper {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  width: 100%;
  background: var(--card-bg-color);
}

.control-panel,
.result-panel {
  padding: 3rem 1.5rem;
  flex-grow: 1;
  width: 100%;
  box-sizing: border-box;
}

.control-panel {
  border-bottom: 4px solid var(--border-color);
}

/*
  --- PANELES DE CONTROLES Y RESULTADOS ---
*/
.card {
  max-width: 950px;
  margin: 0 auto;
  padding: 0;
  box-shadow: none; /* Se elimina la sombra del card para el nuevo diseño */
  background: none; /* Se elimina el fondo del card */
}

.card h2 {
  text-align: left;
  margin-bottom: 2.5rem;
  color: var(--secondary-color);
  font-size: 2.2rem;
  font-weight: 600;
}

/* El resto de estilos de inputs y botones son los mismos */
.inputs-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 2rem;
  margin-bottom: 2.5rem;
}

.input-group,
.radio-group-container {
  display: flex;
  flex-direction: column;
}

.input-group label,
.radio-group-container > label {
  display: block;
  margin-bottom: 0.6rem;
  font-weight: 600;
  color: var (--text-dark);
}

.input-group input {
  width: 100%;
  padding: 0.85rem;
  border: 1px solid var(--border-color);
  border-radius: 10px;
  box-sizing: border-box;
  font-size: 1rem;
  transition: all 0.3s ease;
  background-color: var(--bg-color);
}

.input-group input:focus {
  outline: none;
  border-color: var(--secondary-color);
  box-shadow: 0 0 0 4px rgba(52, 152, 219, 0.25);
  background-color: var(--card-bg-color);
}

.radio-group {
  display: flex;
  gap: 1.5rem;
  align-items: center;
  flex-wrap: wrap;
}

.radio-option {
  display: flex;
  align-items: center;
}

.radio-option input[type="radio"] {
  display: none;
}

.radio-option label {
  position: relative;
  cursor: pointer;
  padding-left: 1.8rem;
  font-size: 1rem;
  user-select: none;
  transition: color 0.3s ease;
  margin-bottom: 0;
}

.radio-option label::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  height: 1.1em;
  width: 1.1em;
  border: 2px solid var(--border-color);
  border-radius: 50%;
  background-color: var(--card-bg-color);
  transition: all 0.3s ease;
}

.radio-option label::after {
  content: '';
  position: absolute;
  left: 0.3em;
  top: 50%;
  transform: translateY(-50%) scale(0);
  height: 0.6em;
  width: 0.6em;
  background-color: var(--secondary-color);
  border-radius: 50%;
  transition: transform 0.3s ease;
}

.radio-option input[type="radio"]:checked + label::before {
  border-color: var(--secondary-color);
}

.radio-option input[type="radio"]:checked + label::after {
  transform: translateY(-50%) scale(1);
}

.radio-option input[type="radio"]:hover + label::before {
  box-shadow: 0 0 0 4px rgba(52, 152, 219, 0.2);
}

.card button {
  width: 100%;
  padding: 1.2rem;
  background: var(--secondary-color);
  color: var(--text-light);
  border: none;
  border-radius: 10px;
  font-size: 1.2rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1.5rem;
}

.card button:hover {
  background: #2980b9;
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.params-box {
  flex: 1;
  max-width: 100%;
  background: var(--bg-color);
  padding: 1.5rem;
  border-radius: 12px;
  border-left: 6px solid var(--success-color);
  box-shadow: var(--shadow-light);
  margin-top: 1.5rem; /* Espaciado superior */
}

.params-box h3, .table-container h3 {
  margin-top: 0;
  margin-bottom: 1.2rem;
  color: var(--success-color);
  font-size: 1.4rem;
}

.params-box p {
  margin: 0.8rem 0;
  font-size: 1rem;
  line-height: 1.5;
}

.params-box b {
  color: var(--primary-color);
}

.table-container {
  flex: 2;
  overflow-x: auto;
  border-radius: 12px;
  box-shadow: var(--shadow-light);
  padding: 1.5rem;
  background: var(--card-bg-color);
}

.result-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  background: var(--card-bg-color);
  border-radius: 12px;
  overflow: hidden;
}

.result-table th,
.result-table td {
  padding: 1.2rem 1rem;
  text-align: center;
  border-bottom: 1px solid var(--bg-color);
}

.result-table th {
  background: var(--secondary-color);
  color: var(--text-light);
  text-transform: uppercase;
  font-weight: 700;
  font-size: 0.95rem;
  position: sticky;
  top: 0;
}

.result-table tbody tr:last-child td {
  border-bottom: none;
}

.result-table tbody tr:hover {
  background-color: var(--bg-color);
}

.result-table tr.extra {
  background: #fff3e0;
  font-weight: bold;
  color: var(--accent-color);
}

.table-warning {
  background-color: #fff3cd; /* Color amarillo claro */
  font-weight: bold;
}

.animate-fade-in {
  animation: fadeIn 0.6s ease-out forwards;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

/*
  --- DISEÑO RESPONSIVO ---
  Ajustes para que el diseño se vea bien en dispositivos móviles y de escritorio.
*/
@media (min-width: 992px) {
  .content-wrapper {
    flex-direction: row;
    height: calc(100vh - 180px); /* Ajusta la altura para que los paneles ocupen el espacio restante */
  }

  .control-panel {
    width: 40%;
    border-bottom: none;
    border-right: 4px solid var(--border-color);
    overflow-y: auto;
  }

  .result-panel {
    width: 60%;
    overflow-y: auto;
    display: flex;
    flex-direction: row;
    gap: 2rem;
  }
}

@media (max-width: 991px) {
  .control-panel,
  .result-panel {
    padding: 2rem 1rem;
  }
  
  .inputs-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
}

.text-warning {
  color: #856404; /* Color amarillo oscuro */
  background-color: #fff3cd; /* Fondo amarillo claro */
  padding: 0.5rem;
  border-radius: 5px;
}
</style>