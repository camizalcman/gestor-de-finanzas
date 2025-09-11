<script setup>

import { ref, computed } from 'vue' //Importo utilidades reactivas

//PROPS
const props = defineProps({
  tipo: { // Ingreso o Gasto
    type: String,
    required: true
  }
})

//EMITS
//Se declaran los eventos que el componente puede emitir para avisar al padre
const emit = defineEmits(['agregar-movimiento', 'cerrar'])

//variables reactivas - estado local
const monto = ref('')
const categoria = ref('')
const nombre = ref('')

//LISTAS DE CATEGORÍAS
const categoriasIngreso = ['Sueldo', 'Freelance', 'Otros']
const categoriasGasto = ['Comida', 'Transporte', 'Servicios', 'Ocio', 'Ropa', 'Otros']

// Según el tipo de movimiento, devuelvo la lista de categorías que corresponde
const categoriasDisponibles = computed(() => {
  if (props.tipo === 'Ingreso') {
    return categoriasIngreso
  } else {
    return categoriasGasto
  }
})

//función que se ejecutará al enviar formulario
function enviarMovimiento () {
  
  if (monto.value === '' || nombre.value.trim() === '' || categoria.value === '') {
    alert('Por favor, completa todos los campos.')
    return
    // Validos que los campos no estén vacíos. Si alguno está vacío, la función termina
  } else {
    const nuevoMovimiento = {
      tipo: props.tipo, // le avisamos si es Ingreso o Gasto
      monto: Number(monto.value), // Convertimos el monto que viene como texto a número
      categoria: categoria.value,
      nombre: nombre.value
    }

    //Avisamos al padre que hay un nuevo gasto y le pasamos los datos
    //nuevoMovimiento es un objeto que viaja al padre
    emit('agregar-movimiento', nuevoMovimiento)

    //vacio los campos
    monto.value = ''
    categoria.value = ''
    nombre.value = ''
  }
  
}

//Cuando el usuario apreta "Cancelar", avisamos al padre para que cierre
  function cancelar() {
    emit('cerrar')
  }

</script>

<template>
   <form @submit.prevent="enviarMovimiento">
    <!-- Cuando se envía el formulario, se ejecuta enviarMovimiento y no recarga la página -->
    <h3>Agregar {{ props.tipo }}</h3>

    <div>
      <label>Nombre:</label>
      <input type="text" v-model="nombre" placeholder="Ingrese el nombre" />
    </div>

    <div>
      <label>Monto:</label>
      <input type="number" v-model="monto" placeholder="Ingrese el monto" />
    </div>

    <div>
      <label>Categoría:</label>
      <!-- v-model="categoria" conecta el valor seleccionado con la variable reactiva "categoria" del componente.-->
      <select v-model="categoria">
        <option value="" disabled>Seleccione una categoría</option>
        <!-- v-for recorre el array categoriasDisponibles y presenta una opcion para cada elemento-->
        <!-- :value="cat" es el valor real que guarda Vue al elegir esa opción-->
        <!-- :key="cat" es un identificador único-->
        <option v-for="cat in categoriasDisponibles" :key="cat" :value="cat">
          {{ cat }}
        </option>
      </select>
    </div>

    <div class="actions">
      <!-- Botón cancelar llama a la función cancelar(), que emite 'cerrar' al padre para cerrar el modal  -->
      <button type="button" @click="cancelar" class="btn-cancel">Cancelar</button>
      <button type="submit">Agregar {{ props.tipo }}</button>
    </div>
  </form>
</template>

<style scoped>
.form-mov {
  display: flex;
  flex-direction: column;
  gap: 10px;
  min-width: 280px;
}
input, select {
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 1em;
  margin-left: 0.6em;
}
.actions {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}
button {
  padding: 8px 12px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
}
button[type="submit"] { background: #FFB143; color: #060028; }
.btn-cancel { background: #d2d2d2; color: #222; }
</style>