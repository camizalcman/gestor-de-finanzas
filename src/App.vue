<script setup>
  import { ref, computed } from 'vue'
  import FormMovimiento from './components/FormMovimientos.vue'

  //Importamos lo necesario de Chart.js y vue-chartjs
  import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement } from 'chart.js'
  import { Pie } from 'vue-chartjs'

  //Registramos los elementos que Chart.js necesita
  ChartJS.register(Title, Tooltip, Legend, ArcElement)

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

  //Calculo de ingresos por categoría
  const ingresosPorCat = computed(() => {
    const resultado = []

      //recorro el array de ingresos
      for (let i = 0; i < ingresos.value.length; i++) {
        const ing = ingresos.value[i]
        let categoriaObj = null

        //recorro el array resultado para ver si ya existe un objeto con la ing.categoría 
        for (let j = 0; j < resultado.length; j++) {
          if (resultado[j].categoria === ing.categoria) {
            categoriaObj = resultado[j]
            break // si la encontré, corto el bucle, categoriaObj deja de ser null y le asigno el objeto correspondiente a esa cat
          }
        }
        
        //Si la categoría ya existe, sumo
        if (categoriaObj) {
          categoriaObj.total += ing.monto
          categoriaObj.porcentaje = (categoriaObj.total * 100) / totalIngresos.value
        } else {
          // Si no existía, creo un objeto nuevo
          resultado.push({
            categoria: ing.categoria,
            total: ing.monto,
            porcentaje: (ing.monto * 100) / totalIngresos.value
          })
        }
      }

    return resultado
  })

  //Calculo de gastos por categoría
  const gastosPorCat = computed(() => {
    const resultado = []

      //recorro el array de ingresos
      for (let i = 0; i < gastos.value.length; i++) {
        const g = gastos.value[i]
        let categoriaObj = null

        //recorro el array resultado para ver si ya existe un objeto con la ing.categoría 
        for (let j = 0; j < resultado.length; j++) {
          if (resultado[j].categoria === g.categoria) {
            categoriaObj = resultado[j]
            break // si la encontré, corto el bucle, categoriaObj deja de ser null y le asigno el objeto correspondiente a esa cat
          }
        }
        
        //Si la categoría ya existe, sumo
        if (categoriaObj) {
          categoriaObj.total += g.monto
          categoriaObj.porcentaje = (categoriaObj.total * 100) / totalGastos.value
        } else {
          // Si no existía, creo un objeto nuevo
          resultado.push({
            categoria: g.categoria,
            total: g.monto,
            porcentaje: (g.monto * 100) / totalGastos.value
          })
        }
      }

    return resultado
  })
  
  //GRÁFICOS
  //Creo un array de colores para cada categoría 
  //La función recibe cuántos colores necesito 
  function generarColores(n) { 
    const paleta = ['#36A2EB','#FF6384','#FFCE56','#4BC0C0','#9966FF','#FF9F40','#C9CBCF','#8AC926','#FF006E','#8338EC'] 
    //Tomo los primeros n colores de la paleta
    return paleta.slice(0, n)
  }

  //Datos del gráfico de torta usando gastosPorCat
  //Esto crea un objeto con la forma que Chart.js espera.
  const gastosTortaData = computed(() => ({
    labels: gastosPorCat.value.map(item => item.categoria), //toma el array gastosPorCat y devuelve un nuevo array solo con los nombres de las categorías.

    //este es un array que Chart.js usa para cada "serie" de datos.
    datasets: [
      {
        data: gastosPorCat.value.map(item => item.total), //toma el array gastosPorCat y devuelve un nuevo array solo con los totales de las categorías. Definen el tamaño de cada porción
        backgroundColor: generarColores(gastosPorCat.value.length)//le paso como parámetro la cant de categorías
      }
    ]
  }))

  //Visualización del gráfico
  const gastosTortaOptions = {
    responsive: true,
    plugins: {
      legend: { position: 'bottom' },
      title: { display: true, text: 'Gastos por categoría' }
    }
  }
  
</script>

<template>
  <main class="w80 pt2-5">
    <div class="contSaldo">
      <h2>Saldo actual ${{ saldo }}</h2>
    </div>

    <!--Totales ingresos y gastos-->
    <section class="resumen">
      <div class="w50">
        <div class="contTotales w100">
          <h2>Total Ingresos ${{ totalIngresos }}</h2>
          <!-- Cuando se hace click, abrimos el modal con tipo 'Ingreso' -->
          <button class="button" @click="abrirModal('Ingreso')">Agregar ingreso</button>
        </div>

        <div class="lista">
          <h2>Lista de ingresos</h2>
          <ul>
            <!-- Recorremos todos los ingresos -->
            <li v-for="(i, indice) in ingresos" :key="indice">
              {{ i.nombre }}: ${{ i.monto }} - {{ i.categoria }}
            </li>
          </ul>
        </div>

        <div class="lista">
          <h2>Lista de ingresos por categoría</h2>
            <ul>
              <li v-for="(item, indice) in ingresosPorCat" :key="indice">
                {{ item.categoria }}: ${{ item.total }} - {{ Math.round(item.porcentaje) }}%
              </li>
            </ul>
        </div>
      </div>
      
      <!--GASTOS-->
      <div class="w50">
        <div class="contTotales w100">
          <h2>Total Gastos ${{ totalGastos }}</h2>
          <!-- Cuando se hace click, abrimos el modal con tipo 'Gasto' -->
          <button class="button" @click="abrirModal('Gasto')">Agregar gasto</button>
        </div>

        <div class="lista">
          <h2>Lista de gastos</h2>
          <ul>
            <!-- Recorremos todos los gastos -->
            <li v-for="(g, indice) in gastos" :key="indice">
              {{ g.nombre }}: ${{ g.monto }} - {{ g.categoria }}
            </li>
          </ul>
        </div>

        <div class="lista">
          <h2>Lista de gastos por categoría</h2>
          <Pie :data="gastosTortaData" :options="gastosTortaOptions" />
            <ul>
              <li v-for="(item, indice) in gastosPorCat" :key="indice">
                {{ item.categoria }}: ${{ item.total }} - {{ Math.round(item.porcentaje) }}%
              </li>
            </ul>
        </div>
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
  background-color: #65EEC3;
  box-shadow: 0 0 8px 5px rgba(255, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1em;
  color:#060028;
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
  background-color: #FFB143;
  box-shadow: 0 0 8px 5px rgba(255, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1em;
  color:#060028;
  margin-bottom: 1em;
  font-family: "Plus Jakarta Sans", sans-serif;
  font-weight: 700;
  display: flex;
  justify-content: space-between;
}

.button{
  padding: 10px 14px; 
  border-radius: 8px; 
  border-color: #060028; 
  background: #FFB143;
  cursor: pointer; 
  color: #060028; 
  font-family: "Plus Jakarta Sans", sans-serif;
}

.lista{
  border: thin solid #FFB143;
  box-shadow: 0 0 8px 5px rgba(255, 255, 255, 0.3);
  border-radius: 8px; 
  padding: 1em;
  color: white;
  font-family: "Plus Jakarta Sans", sans-serif;
  margin-bottom: 1.6em;
}

ul { 
  margin: 0; 
  padding: 0; 
  list-style: none; 
}

li { 
  padding: 12px 0; 
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
