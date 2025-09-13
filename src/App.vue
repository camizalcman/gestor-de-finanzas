<script setup>
  import { ref, computed } from 'vue'
  import FormMovimiento from './components/FormMovimientos.vue'
  import Ingresos from './components/Ingresos.vue'
  import Gastos from './components/Gastos.vue'

  //ESTADO GLOBAL
  //Guardo todos los movimientos (ingresos y gastos) en un solo array
  const movimientos = ref([])
  
  // Control del modal: null = cerrado, 'Ingreso' = abrir ingreso, 'Gasto' = abrir gasto
  const modalTipo = ref(null)

    //Funciones para controlar el modal
  function abrirModal(tipo) {
    // Abrimos el modal y definimos si es ingreso o gasto
    modalTipo.value = tipo
  }

  function cerrarModal() {
    // Cerramos el modal
    modalTipo.value = null
  }

  //Manejo del evento emitido por el hijo
  function agregarMovimiento(nuevo) {
    //"nuevo" es un objeto con { tipo, monto, categoria, nombre }
    movimientos.value.push(nuevo)

    // Cerramos el modal automáticamente
    cerrarModal()
  }

  //Filtrar movimientos por tipo
  const ingresos = computed(() => movimientos.value.filter(mov => mov.tipo === 'Ingreso'))
  const gastos = computed(() => movimientos.value.filter(mov => mov.tipo === 'Gasto'))

  //Cálculo de ingresos y gastos totales de forma reactiva
  const totalIngresos = computed(() => {
    let suma = 0
    for (let i = 0; i < ingresos.value.length; i++) {
      suma += ingresos.value[i].monto
    }
    return suma
  })

  const totalGastos = computed(() => {
    let suma = 0
    for (let i = 0; i < gastos.value.length; i++) {
      suma += gastos.value[i].monto
    }
    return suma
  })
  
  //Cálculo de saldo
  const saldo = computed(() => totalIngresos.value - totalGastos.value)

  </script>


<template>
  <main class="w80 pt2-5">
    <!-- SALDO -->
    <section class="contSaldo">
      <h2>Saldo actual ${{ saldo }}</h2>
    </section>

    <!--Totales ingresos y gastos-->
    <section class="resumen">
      <!-- INGRESOS -->
      <div class="w50">
        <div class="contTotales w100">
          <h2>Total Ingresos ${{ totalIngresos }}</h2>
          <!-- Cuando se hace click, abrimos el modal con tipo 'Ingreso' -->
          <button class="button" @click="abrirModal('Ingreso')">Agregar ingreso</button>
        </div>

        <!-- Componente Ingresos-->
        <!-- Le paso los datos al componente. Los : significa que paso una variable de JS -->
        <Ingresos
          :ingresos="ingresos" 
          :totalIngresos="totalIngresos"
        />
      </div>
      
      <!-- GASTOS -->
      <div class="w50">
        <div class="contTotales w100">
          <h2>Total Gastos ${{ totalGastos }}</h2>
          <!-- Cuando se hace click, abrimos el modal con tipo 'Gasto' -->
          <button class="button" @click="abrirModal('Gasto')">Agregar gasto</button>
        </div>

        <!-- Componente Gastos -->
        <!-- Le paso los datos al componente. Los : significa que paso una variable de JS -->
        <Gastos 
          :gastos="gastos"
          :totalGastos="totalGastos"
        />
      </div>
    </section>


    <!-- MODAL -->
    <!-- Solo se muestra si modalTipo no es null -->
    <div v-if="modalTipo" class="modal-overlay" @click.self="cerrarModal">
      <div class="modal">
        <!-- Componente hijo -->
        <!-- El padre está obligado a pasarle un valor al hijo cuando usa este comp.-->
        <!-- : es la sintaxis de Vue para pasar el valor de una variable de JS (no un texto)".-->
        <!--Cuando el hijo emite el evento agregar-movimiento, el padre ejecuta la funcion agregarMovimiento (que agrega el movimiento al array movimientos). El @ escucha eventos-->
        <FormMovimiento 
          :tipo="modalTipo"
          @agregar-movimiento="agregarMovimiento" 
          @cerrar="cerrarModal"
        />
      </div>
    </div>
  </main>
</template>

<style scoped>
.w100{
  width: 100%;
}

.w80{
  width: 80%;
}

.w50{
  width: 50%;
}

.bordeRojo{
  border: solid 2px red;
}

.pt2-5{
  padding-top: 2.5em;
}

.contSaldo{
  background-color:#489fb5;
  box-shadow: 0 0 8px 5px rgba(255, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1em;
  color:#030017;
  margin-bottom: 2em;
  font-family: "Plus Jakarta Sans", sans-serif;
  font-weight: 800;
}

main { 
  margin: 0 auto; 
}

.resumen { 
  display: flex; 
  gap: 20px; 
  align-items: baseline; 
}

.contTotales{
  background-color: #ffa62b;
  box-shadow: 0 0 8px 5px rgba(255, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1em;
  color:#030017;
  margin-bottom: 1em;
  font-family: "Plus Jakarta Sans", sans-serif;
  font-weight: 700;
  display: flex;
  justify-content: space-between;
}

.button{
  padding: 10px 14px; 
  border-radius: 8px; 
  border-color: #030017; 
  background: #ffa62b;
  cursor: pointer; 
  color:#030017; 
  font-family: "Plus Jakarta Sans", sans-serif;
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
  min-width: 340px;
}
</style>
