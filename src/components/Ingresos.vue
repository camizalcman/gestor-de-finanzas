<script setup>
import { computed } from 'vue' //Importo utilidades reactivas

//Importo lo necesario de Chart.js y vue-chartjs
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement } from 'chart.js'
import { Pie } from 'vue-chartjs'

//Registramos los elementos que Chart.js necesita
ChartJS.register(Title, Tooltip, Legend, ArcElement)

//Defino la props, datos que el padre le pasa al componente
const props = defineProps({
    ingresos: Array, //array con todos los ingresos
    totalIngresos: Number //Monto total de todos los ingresos (calculado en App.vue)
})

//Calculo de ingresos por categoría
const ingresosPorCat = computed(() => {
    const resultado = []

    //recorro el array de ingresos
    for (let i = 0; i < props.ingresos.length; i++) {
        const ing = props.ingresos[i]
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
            categoriaObj.porcentaje = (categoriaObj.total * 100) / props.totalIngresos
        } else {
            // Si no existía, creo un objeto nuevo
            resultado.push({
                categoria: ing.categoria,
                total: ing.monto,
                porcentaje: (ing.monto * 100) /  props.totalIngresos
            })
        }
    }

    return resultado
})

//GRÁFICO INGRESOS
  function generarColoresIng(n) { 
    const paleta = ["#FFB4A2", "#83c5be", "#457B9D", "#E76F51","#F4A261","#8AB17D"];
    //Tomo los primeros n colores de la paleta
    return paleta.slice(0, n)
  }

  //Datos del gráfico de torta usando ingresosPorCat
  //Esto crea un objeto con la forma que Chart.js espera.
  const ingresosTortaData = computed(() => ({
    labels: ingresosPorCat.value.map(item => item.categoria), //toma el array gastosPorCat y devuelve un nuevo array solo con los nombres de las categorías.

    //este es un array que Chart.js usa para cada "serie" de datos.
    datasets: [
      {
        data: ingresosPorCat.value.map(item => item.total), //toma el array gastosPorCat y devuelve un nuevo array solo con los totales de las categorías. Definen el tamaño de cada porción
        backgroundColor: generarColoresIng(ingresosPorCat.value.length)//le paso como parámetro la cant de categorías
      }
    ]
  }))

  //Visualización del gráfico
  const ingresosTortaOptions = {
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
            <Pie :data="ingresosTortaData" :options="ingresosTortaOptions" />
        </div>
        <ul>
            <li v-for="(item, indice) in ingresosPorCat" :key="indice">
                {{ item.categoria }}: ${{ item.total }} - {{ Math.round(item.porcentaje) }}%
            </li>
        </ul>
    </div>

    <div class="lista">
        <h2>Lista de ingresos</h2>
        <ul>
            <!-- Recorremos todos los ingresos -->
            <li v-for="(i, indice) in props.ingresos" :key="indice">
                {{ i.nombre }}: ${{ i.monto }} - {{ i.categoria }}
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