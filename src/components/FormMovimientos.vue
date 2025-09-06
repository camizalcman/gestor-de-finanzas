<script setup>

import { ref } from 'vue' //Importo utilidades reactivas

const props = defineProps({
  tipo: { // Ingreso o Gasto
    type: String,
    required: true
  }
})


//Se declaran los eventos que el componente puede emitir para avisar al padre
//Emit es una función que permite avisar al componente padre que ocurrió un evento
const emit = defineEmits(['agregar-movimiento']);

//variables reactivas
const monto = ref('')
const categoria = ref('')
const nombre = ref('')

//función que se ejecutará al enviar formulario
function enviarMovimiento () {
  
  if (monto.value === '' || nombre.value.trim() === ''){
    return
    // Validos que los campos no estén vacíos. Si alguno está vacío, la función termina
  } else {
    const nuevoGasto = {
      tipo: props.tipo, // le avisamos si es Ingreso o Gasto
      monto: Number(monto.value), // Convertimos el monto que viene como texto a número
      categoria: categoria.value,
      nombre: nombre.value
    }

    emit('agregar-movimiento', nuevoGasto)
    // Avisamos al padre que hay un nuevo gasto y le pasamos los datos

    //vacio los campos
    monto.value = ''
    categoria.value = ''
    nombre.value = ''
  }
  
}

</script>

<template>
   <form @submit.prevent="enviarMovimiento">
    <!-- Cuando se envía el formulario, se ejecuta enviarMovimiento y no recarga la página -->

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
      <input type="text" v-model="categoria" placeholder="Ingrese la categoría" />
    </div>

    <button type="submit">Agregar</button>
    <!-- Mostramos el tipo dinámicamente -->
  </form>
</template>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 300px;
  margin: 20px auto;
}

input {
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 8px;
  background-color: #00162e;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>