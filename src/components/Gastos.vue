<script setup>
import { computed } from 'vue' //Importo utilidades reactivas
import CategoriaDetalle from './CategoriaDetalle.vue'


//Importo lo necesario de Chart.js y vue-chartjs
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement} from 'chart.js'
import { Pie } from 'vue-chartjs'
import ChartDataLabels from 'chartjs-plugin-datalabels';

//Registro los elementos que Chart.js necesita
ChartJS.register(Title, Tooltip, Legend, ArcElement, ChartDataLabels);


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
          categoriaObj.total += g.monto,
          categoriaObj.porcentaje = (categoriaObj.total * 100) / props.totalGastos,
          categoriaObj.movimientos.push(g)
        } else {
          // Si no existía, creo un objeto nuevo
          resultado.push({
            categoria: g.categoria,
            total: g.monto,
            porcentaje: (g.monto * 100) / props.totalGastos,
            movimientos:[g] //creo un array dentro de cada categoría con todos los movimientos que le corresponden
          })
        }
      }

    return resultado
  })
  
  //GRÁFICO GASTOS
    //Creo un array de colores para cada categoría 
    //La función recibe cuántos colores necesito 
    function generarColores(n) { 
        const paleta = ["#E76F51","#82c0cc","#264653","#F4A261","#8AB17D","#E9C46A","#A8DADC","#457B9D","#FFB4A2","#6D6875"];
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
                data: gastosPorCat.value.map(item => item.porcentaje), //toma el array gastosPorCat y devuelve un nuevo array solo con los totales de las categorías. Definen el tamaño de cada porción
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
          tooltip: {
            callbacks: {
              label: function(context) {
                const index = context.dataIndex;
                const categoria = gastosPorCat.value[index];
                const monto = categoria.total.toLocaleString('es-AR', {
                  style: 'currency',
                  currency: 'ARS',
                  minimumFractionDigits: 0,
                  maximumFractionDigits: 0
                });
                return `${monto}`;
              }
            }
          },
          datalabels: {
                formatter: (value) => {
                    //El 'value' es el porcentaje calculado.
                    //Esta línea le agrega el símbolo.
                    return value.toFixed(1) + '%';
                },
                color: '#fff', 
                font: {
                    weight: 'bold',
                }
            }
        }
    }

</script>

<template>
<section>
    <div class="w100">
          <div class="torta contGrafico" style="width: 100%; height: 100%;">
            <Pie :data="gastosTortaData" :options="gastosTortaOptions" />
          </div>

             <!-- Recorro categorías e inserto el componente hijo -->
            <ul>
                <CategoriaDetalle
                    v-for="cat in gastosPorCat"
                    :key="cat.categoria"
                    :categoria="cat.categoria"
                    :total="cat.total"
                    :porcentaje="cat.porcentaje"
                    :movimientos="cat.movimientos"
                />
            </ul>
        </div>
</section>
</template>

<style scoped>
.bordeRojo{
  border: solid 2px red;
}

.w100{
  width: 100%;
}

.contGrafico{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  border-radius: 8px; 
  padding: 1em;
  color:#16697a;
  font-family: "Plus Jakarta Sans", sans-serif;
  margin-bottom: 1.6em; 
  width: 100%;
}

.torta{
    margin-bottom: 1em;
}

ul { 
  margin: 0; 
  padding: 0; 
  list-style: none;
    width: 100%;
}

</style>