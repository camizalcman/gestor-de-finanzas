<script>
//Exporto el componente para que pueda ser utilizado en App.vue
export default{
    name: 'FormGastos', //Nombre del componente

    data() {
        return {
            //Defino las variables reactivas
            monto: '',       
            categoria: '',    
        }
    },

    methods: {
    //Defino las funciones del componente
    
        enviarGasto() {
            //Se ejecutará cuando el usuario envíe el formulario
            //Valido que no estén vacíos los campos
            if (!this.monto || !this.categoria){
                return
            } 

            //Creo un objeto con los datos ingresados
            const nuevoGasto = {
                monto: Number(this.monto),  
                categoria: this.categoria
            }

            // Avisamos al componente padre App.vue que hay un nuevo ingreso
            this.$emit('agregar-gasto', nuevoGasto) //nombre del evento, objeto con datos

            //limpio los datos del formulario
            this.monto = ''
            this.categoria = ''
        }
    }

}
</script>

<template>
  <form @submit.prevent="enviarGasto"> <!--La función que se ejecuta al enviar-->
    <div>
      <label>Monto:</label>
      <input type="number" v-model="monto" placeholder="Ingrese el monto"> <!-- v-model="monto" se vincula el input con la variable del data. Cada vez que escribís algo, monto o categoria se actualiza automáticamente.-->
    </div>

    <div>
      <label>Categoría:</label>
      <input type="text" v-model="categoria" placeholder="Ingrese la categoría">
    </div>

    <button type="submit">Agregar gasto</button>
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