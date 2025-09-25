<script setup>
import { ref } from 'vue'
import { XCircleIcon } from '@heroicons/vue/24/outline'

//Defino la props, datos que el padre le pasa al componente
const props = defineProps({
    categoria: { type: String, required: true },
    total: { type: Number, required: true },
    porcentaje: { type: Number, required: true },
    movimientos: { type: Array, default: () => [] } //Array de movimientos de esa categoría
})

const mostrarModal = ref(false)

function abrirModal() {
  mostrarModal.value = true
}

function cerrarModal() {
  mostrarModal.value = false
}

</script>

<template>
    <li class="categoria-item">
        <div class="categoria-header" @click="toggle">
          <p class="w25">{{ categoria }}</p>
          <p class="w25">{{ Math.round(porcentaje) }}%</p>
          <p class="w25">${{ total }}</p>
          <button class="button" @click="abrirModal()">Ver detalle</button>

          <div v-if="mostrarModal" class="modal-overlay" @click.self="cerrarModal">
            <div class="modal w30 w50t w80m">
              <div class="modalArriba">
                <h3 class="titulo">Detalle de {{ categoria }}</h3>
                <button class="cerrar" @click="cerrarModal"><XCircleIcon style="width: 1.8em; height: 1.8em;"/></button>
              </div>
              <ul>
                <li v-for="(mov, i) in movimientos" :key="i" class="itemDetalle">
                  {{ mov.nombre }} : ${{ mov.monto }}
                </li>
              </ul>
  
            </div>
          </div>

        </div>
  </li>
</template>

<style scoped>
.categoria-item {
  overflow: hidden;
  font-family: "Plus Jakarta Sans", sans-serif;
  width: 100%;
  border-radius: 10px;
  border: solid 2px #ffa62b;
  margin-bottom: 1em;
}

.categoria-header {
  padding: 12px 12px;
  cursor: pointer;
  color: #030017;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.categoria-detalle {
   color: #030017;
  list-style: none;
}

.w25{
  width: 25%;
}

.w30{
  width: 30%;
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

/* MODAL */
.modal-overlay {
  position: fixed;
  inset: 0; 
  background: rgba(0,0,0,0.45);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 40;
}
.modal {
  background: white;
  border-radius: 10px;
  padding: 2em;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
}

.button{
  padding: 5px 10px; 
  border-radius: 8px; 
  border-color: #489fb5; 
  background: #489fb5;
  cursor: pointer; 
  color:#ffffff; 
  font-family: "Plus Jakarta Sans", sans-serif;
  font-size: 0.8em;
}

.cerrar{
  color:#07022d; 
  display: flex;
  align-items: center;
  justify-content: center;
  background: none;
  border: none;
  cursor: pointer;
}

.itemDetalle{
  list-style: none;
}

.titulo{
  font-weight: bold;
  font-size:large;
}

.modalArriba{
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 0.8em;
}

/* TABLET */
@media (max-width: 1024px) and (min-width: 768px) {
 .w50t{
    width: 50%;
  }
}

/* MOBILE */
@media (max-width: 767px) {
  .w80m{
    width: 80%;
  }
}

</style>