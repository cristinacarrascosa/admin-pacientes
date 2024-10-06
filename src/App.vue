
<script setup>
import { ref, reactive, onMounted, watch } from 'vue'
import { uid } from 'uid'
import Header from './components/Header.vue'
import Formulario from './components/Formulario.vue'
import Paciente from './components/Paciente.vue'

const pacientes = ref([])

const paciente = reactive({
        id: null,
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: ''
    })

//cada vez que pacientes cambie ejecutamos esta funcion
watch(pacientes, () => {
    guadarLocalStorage()
}, {
    deep: true//para que tome en cuenta los cambios del objeto
})

// almacenamos los pacientes en localStorage
const guadarLocalStorage = () => {
    localStorage.setItem('pacientes', JSON.stringify(pacientes.value))
}

// cargamos los pacientes
onMounted(() => {
    const pacienteStorage = localStorage.getItem('pacientes')
    if (pacienteStorage) {
        pacientes.value = JSON.parse(pacienteStorage)
    }
})

const guardarPaciente = () => {
    if (paciente.id) {
        //aplicamos destruturing al ID
        const { id } = paciente
        //findIndex es un método que solamente fuciona con arreglos y nos permite encontra el índice
         //entramos en cada uno de los elementos y creamos una variable temporal
        const i = pacientes.value.findIndex((paciente) => paciente.id === id)
        //tomamos una copia de ese paciente y reescribimos los datos
        pacientes.value[i] = {...paciente}
    } else {
         // Agregar el paciente y crear una nueva instancia
    pacientes.value.push({ 
        ...paciente,// Desestructuración para evitar referencias al mismo objeto
        id: uid() // llama al import que genera un id único
     }) 
    }
    // Reiniciar el formulario
    Object.assign(paciente, {
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: '',
        id: null
        })
    }

    const editarPaciente = (id) => {
        const pacienteEditar = pacientes.value.filter(
            // me permite filtrar del array por el id
            paciente => paciente.id === id)[0] 
            // le asigno al Formulario el objeto que he filtrado
        Object.assign(paciente, pacienteEditar) 
    }

    const eliminarPaciente = (id) => {
        pacientes.value = pacientes.value.filter( paciente => paciente.id !== id)
    }


   

</script>
<template>
 <div class="container mx-auto mt-20">
    <Header />

    <div class="mt-12 md:flex">
      <Formulario
      v-model:nombre = "paciente.nombre"
      v-model:propietario = "paciente.propietario"
      v-model:email = "paciente.email"
      v-model:alta = "paciente.alta"
      v-model:sintomas = "paciente.sintomas"
      @guardar-paciente="guardarPaciente"
      :id="paciente.id"
      />

        <div class="md:w-1/2 md:h-screen overflow-y-scroll">
            <h3 class="font-black text-3xl text-center">Administra tus Pacientes</h3>

            <div v-if="pacientes.length > 0">

                <p class="text-lg mt-5 text-center mb-10">
                 Información de
                     <span class="text-indigo-600 font-bold">Pacientes</span>
                </p>

                <Paciente 
                v-for="paciente in pacientes"
                :paciente="paciente"
                @editar-paciente="editarPaciente"
                @eliminar-paciente="eliminarPaciente"
                />
            
            </div>
            <p v-else class="mt-10 text-2xl text-center">No Hay Pacientes</p>
        </div>  
   
    </div>
 </div>
</template>


