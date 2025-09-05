<script setup>

import { ref } from 'vue' //Importo utilidades reactivas

//Declaro variables reactivas
const monto = ref('') 
const categoria = ref('')

//Se declaran los eventos que el componente puede emitir para avisar al padre
//Emit es una función que permite avisar al componente padre que ocurrió un evento
const emit = defineEmits(['agregar-gasto']);

//función que se ejecutará al enviar formulario
function enviarGasto () {
  
  if (monto.value === '' || categoria.value.trim() === ''){
    return
    // Validos que los campos no estén vacíos. Si alguno está vacío, la función termina
  } else {
    const nuevoGasto = {
      monto: Number(monto.value),         
      // Convertimos el monto que viene como texto a número
      categoria: categoria.value.trim()   
      // Limpiamos espacios al inicio o final de la categoría
    }

  emit('agregar-gasto', nuevoGasto)
  // Avisamos al padre que hay un nuevo gasto y le pasamos los datos

  monto.value = ''
  // Reiniciamos el campo monto para que quede vacío
  categoria.value = ''
  // Reiniciamos el campo categoría para que quede vacío
  }
  
}

</script>

<template>
  <form @submit.prevent="enviarGasto">
    <!-- Cuando se envía el formulario, se ejecuta la función enviarGasto y no se recarga la página -->

    <div>
      <label>Monto:</label>
      <input type="number" v-model="monto" placeholder="Ingrese el monto" />
      <!-- v-model conecta el input con la variable reactiva monto. Cada cambio en el input actualiza monto automáticamente -->
    </div>

    <div>
      <label>Categoría:</label>
      <input type="text" v-model="categoria" placeholder="Ingrese la categoría" />
      <!-- v-model conecta el input con la variable reactiva categoria -->
    </div>

    <button type="submit">Agregar gasto</button>
    <!-- Botón para enviar el formulario -->
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