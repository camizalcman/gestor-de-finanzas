<script setup>
import { computed } from 'vue' //Importo utilidades reactivas

//Importo lo necesario de Chart.js y vue-chartjs
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement } from 'chart.js'
import { Pie } from 'vue-chartjs'

//Registramos los elementos que Chart.js necesita
ChartJS.register(Title, Tooltip, Legend, ArcElement)

//Defino la props, datos que el padre le pasa al componente
const props = defineProps({
    gastos: Array, //array con todos los gastos
    totalGastos: Number //Monto total de todos los gastos (calculado en App.vue)
})

//Calculo de gastos por categoría
  const gastosPorCat = computed(() => {
    const resultado = []

      //recorro el array de gastos
      for (let i = 0; i < props.gastos.length; i++) {
        const g = props.gastos[i]
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
          categoriaObj.porcentaje = (categoriaObj.total * 100) / props.totalGastos
        } else {
          // Si no existía, creo un objeto nuevo
          resultado.push({
            categoria: g.categoria,
            total: g.monto,
            porcentaje: (g.monto * 100) / props.totalGastos
          })
        }
      }

    return resultado
  })
  
  //GRÁFICO GASTOS
    //Creo un array de colores para cada categoría 
    //La función recibe cuántos colores necesito 
    function generarColores(n) { 
        const paleta = ["#82c0cc","#E76F51","#264653","#F4A261","#8AB17D","#E9C46A","#A8DADC","#457B9D","#FFB4A2","#6D6875"];
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
        maintainAspectRatio: false,
        plugins: {
            legend: { position: 'bottom' },
        }
    }

  
</script>

<template>
<section>
    <div class="contGrafico w100">
          <div style="width: 80%; height: 80%;">
            <Pie :data="gastosTortaData" :options="gastosTortaOptions" />
          </div>
            <ul>
              <li v-for="(item, indice) in gastosPorCat" :key="indice">
                {{ item.categoria }}: ${{ item.total }} - {{ Math.round(item.porcentaje) }}%
              </li>
            </ul>
        </div>

        <div class="lista">
          <h2>Lista de gastos</h2>
          <ul>
            <!-- Recorremos todos los gastos -->
            <li v-for="(g, indice) in props.gastos" :key="indice">
              {{ g.nombre }}: ${{ g.monto }} - {{ g.categoria }}
            </li>
          </ul>
        </div>
</section>
</template>

<style scoped>
.contGrafico{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  border: thin solid #16697a;
  box-shadow: 0 0 10px 2px rgba(29, 29, 29, 0.2);
  border-radius: 8px; 
  padding: 1em;
  color:#16697a;
  font-family: "Plus Jakarta Sans", sans-serif;
  margin-bottom: 1.6em; 
}

.lista{
  border: thin solid #16697a;
  box-shadow: 0 0 10px 2px rgba(29, 29, 29, 0.2);
  border-radius: 8px; 
  padding: 1em;
  color:#16697a;
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
</style>