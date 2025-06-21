<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const nombreCompleto = ref('')
const correo = ref('')
const telefono = ref('')
const mensaje = ref('')

const enviando = ref(false)
const exito = ref(false)
const error = ref('')

const enviarFormulario = async () => {
  exito.value = false
  error.value = ''
  enviando.value = true

  try {
    const respuesta = await axios.post('http://localhost:3000/api/contacto', {
      nombreCompleto: nombreCompleto.value,
      correo: correo.value,
      telefono: telefono.value,
      mensaje: mensaje.value,
    })

    if (respuesta.status === 201) {
      exito.value = true
      nombreCompleto.value = ''
      correo.value = ''
      telefono.value = ''
      mensaje.value = ''
    }
  } catch (err: any) {
    error.value = err.response?.data?.error || 'Error al enviar el formulario'
  } finally {
    enviando.value = false
  }
}
</script>

<template>
  <div class="h-screen flex items-center justify-center">
    <div class="relative p-10 bg-white rounded-2xl shadow-lg max-w-lg w-full">
      <div class="absolute inset-0 -z-10 transform rotate-6 bg-green-500 rounded-2xl"></div>

      <h2 class="text-xl font-semibold text-gray-800">
        <span class="text-green-600 font-bold">¿Necesitas ayuda?</span> Por favor completa el
        formulario.
      </h2>

      <form class="mt-5 space-y-4" @submit.prevent="enviarFormulario">
        <div>
          <label class="block font-medium text-gray-700">Nombre completo*</label>
          <input
            v-model="nombreCompleto"
            type="text"
            placeholder="Tu nombre"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-green-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Correo*</label>
          <input
            v-model="correo"
            type="email"
            placeholder="Tu correo"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-green-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Teléfono*</label>
          <input
            v-model="telefono"
            type="tel"
            placeholder="Tu número"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-green-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Mensaje*</label>
          <textarea
            v-model="mensaje"
            placeholder="Escribe tu mensaje"
            rows="4"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-green-500 outline-none"
            required
          ></textarea>
        </div>

        <button
          type="submit"
          :disabled="enviando"
          class="w-full py-3 text-white bg-green-600 rounded-md hover:bg-green-700 transition"
        >
          {{ enviando ? 'Enviando...' : 'Enviar mensaje' }}
        </button>

        <!-- Mensajes -->
        <p v-if="exito" class="text-green-600 font-medium mt-2">¡Formulario enviado con éxito!</p>
        <p v-if="error" class="text-red-600 font-medium mt-2">{{ error }}</p>
      </form>
    </div>
  </div>
</template>
