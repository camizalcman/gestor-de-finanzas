<script setup>
import { ref } from 'vue'

//Defino la props, datos que el padre le pasa al componente
const props = defineProps({
    categoria: { type: String, required: true },
    total: { type: Number, required: true },
    porcentaje: { type: Number, required: true },
    movimientos: { type: Array, default: () => [] } //Array de movimientos de esa categoría
})

//Estado local para abrir/cerrar el detalle
const abierto = ref(false) //Variable reactiva

function toggle() {
  abierto.value = !abierto.value
}

</script>

<template>
    <li class="categoria-item">
        <div class="categoria-header" @click="toggle">
          {{ categoria }}: ${{ total }} — {{ Math.round(porcentaje) }}%
           <span class="flecha">
            {{ abierto ? '↑' : '↓' }}
          </span>
        </div>

    <ul v-if="abierto" class="categoria-detalle">
      <li v-for="(mov, i) in movimientos" :key="i">
        {{ mov.nombre }} : ${{ mov.monto }}
      </li>
    </ul>
  </li>
</template>

<style scoped>
.categoria-item {
  overflow: hidden;
  font-family: "Plus Jakarta Sans", sans-serif;
  width: 100%;
  border-radius: 10px;
  background-color: #82C0CC;
  margin-bottom: 1em;
}

.categoria-header {
  padding: 12px 12px;
  cursor: pointer;
  color: #030017;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
}

.categoria-detalle {
   color: #030017;
  list-style: none;
}

.categoria-detalle li {
  width: 100%;
  padding: 6px 12px;
}

.flecha{
  font-weight: 700;
  width: 10%;
  display: flex;
  justify-content: center;
}

</style>