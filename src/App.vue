<script>
  import Ingreso from './components/FormIngresos.vue'
  import Gasto from './components/FormMovimientos.vue'


  export default {
      name: 'App',
      components: {
        Ingreso, //Lo registramos para poder usarlo en el template
        Gasto
    },

    data() {
      return {
        ingresos: [], //Para guardar todos los ingresos
        gastos: []
      }
    },

    methods: {
      agregarIngreso(nuevoIngreso) {
        // Agregamos el ingreso que viene del hijo al array de ingresos
        this.ingresos.push(nuevoIngreso)
      },

      agregarGasto(nuevoGasto) {
        // Agregamos el gasto que viene del hijo al array de gastos
        this.gastos.push(nuevoGasto)
      }
    }
  }
 
</script>

<template>
  <div class="contenedor df">
    <div>
      <h1>Ingresos</h1>
      <Ingreso @agregar-ingreso="agregarIngreso"/> <!-- Aca estoy agregando el componente Ingreso. @agregar-ingreso escucha el evento que el hijo emite con $emit y agregarIngreso es el método del padre que se ejecuta (definido acá) cuando ocurre ese evento -->
      <h2>Lista de ingresos:</h2>
      <ul>
        <li v-for="ingreso in ingresos"><!-- v-for recorre el array-->
          {{ ingreso.monto }} - {{ ingreso.categoria }}
        </li>
      </ul>
    </div>

    <div>
      <h1>Gastos</h1>
      <Gasto @agregar-gasto="agregarGasto"/> <!-- @agregar-gasto escucha el evento que el hijo emite con $emit y agregarGasto es el método del padre que se ejecuta (definido acá) cuando ocurre ese evento -->
      <h2>Lista de gastos:</h2>
      <ul>
        <li v-for="gasto in gastos"><!-- v-for recorre el array-->
          {{ gasto.monto }} - {{ gasto.categoria }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.df{
  display: flex;
}
.contenedor{
  gap:2em
}


</style>
