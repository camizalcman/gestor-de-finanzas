<script>
  import Ingreso from './components/FormIngresos.vue'

  export default {
      name: 'App',
      components: {
        Ingreso //Lo registramos para poder usarlo en el template
    },

    data() {
      return {
        ingresos: [] //Para guardar todos los ingresos
      }
    },

    methods: {
      agregarIngreso(nuevoIngreso) {
        // Agregamos el ingreso que viene del hijo al array de ingresos
        this.ingresos.push(nuevoIngreso)
      }
    }
  }
 
</script>

<template>
  <div>
    <h1>Gestor de Ingresos</h1>
    <Ingreso @agregar-ingreso="agregarIngreso"/> <!-- @agregar-ingreso escucha el evento que el hijo emite con $emit y agregarIngreso es el método del padre que se ejecuta (definido acá) cuando ocurre ese evento -->
    <h2>Lista de ingresos:</h2>
    <ul>
      <li v-for="ingreso in ingresos"><!-- v-for recorre el array-->
        {{ ingreso.monto }} - {{ ingreso.categoria }}
      </li>
    </ul>
  </div>
</template>

<style scoped>


</style>
