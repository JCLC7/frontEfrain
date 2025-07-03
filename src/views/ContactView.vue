<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

const siteKey = import.meta.env.VITE_RECAPTCHA_SITE_KEY

const nombreCompleto = ref('')
const correo = ref('')
const telefono = ref('')
const mensaje = ref('')
const terminos = ref(false)

const enviando = ref(false)
const exito = ref(false)
const error = ref('')

// Esta variable almacenará el ID del widget para poder resetearlo
const captchaWidgetId = ref<number | null>(null)

// onMounted se ejecuta cuando el componente está listo en el DOM
onMounted(() => {
  // Función para renderizar el captcha.
  const renderCaptcha = () => {
    if ((window as any).grecaptcha && document.getElementById('captcha-container')) {
      captchaWidgetId.value = (window as any).grecaptcha.render('captcha-container', {
        sitekey: siteKey,
      })
    }
  }

  // Verifica si grecaptcha ya está disponible. Si no, espera a que cargue.
  if ((window as any).grecaptcha) {
    renderCaptcha()
  } else {
    // Si el script aún no ha cargado, espera al evento 'load'.
    // Esto es más eficiente que setInterval.
    window.addEventListener('load', renderCaptcha)
  }
})

const enviarFormulario = async () => {
  error.value = ''
  exito.value = false

  // Validaciones de campos
  if (!nombreCompleto.value.trim() || !/^[a-zA-Z\u00C0-\u017F\s'-]+$/.test(nombreCompleto.value)) {
    error.value = 'Por favor, introduce un nombre válido.'
    return
  }
  if (!correo.value || !/^\S+@\S+\.\S+$/.test(correo.value)) {
    error.value = 'Por favor, introduce un correo electrónico válido.'
    return
  }
  if (!telefono.value || !/^[0-9+\-() ]{7,15}$/.test(telefono.value)) {
    error.value = 'Por favor, introduce un número de teléfono válido.'
    return
  }
  if (!mensaje.value.trim()) {
    error.value = 'Por favor, escribe un mensaje.'
    return
  }
  if (!terminos.value) {
    error.value = 'Debes aceptar los términos y condiciones para continuar.'
    return
  }

  enviando.value = true

  const grecaptcha = (window as any).grecaptcha
  // La obtención del token es correcta
  const token = captchaWidgetId.value !== null ? grecaptcha.getResponse(captchaWidgetId.value) : ''

  if (!token) {
    error.value = 'Por favor completa el captcha.'
    enviando.value = false
    return
  }

  try {
    const respuesta = await axios.post('https://backefrain.onrender.com/api/contacto', {
      nombreCompleto: nombreCompleto.value,
      correo: correo.value,
      telefono: telefono.value,
      mensaje: mensaje.value,
      recaptchaToken: token,
    })

    if (respuesta.status === 201) {
      exito.value = true
      // Limpia el formulario
      nombreCompleto.value = ''
      correo.value = ''
      telefono.value = ''
      mensaje.value = ''
      terminos.value = false
      // Resetea el captcha
      if (captchaWidgetId.value !== null) {
        grecaptcha.reset(captchaWidgetId.value)
      }
    }
  } catch (err: any) {
    error.value = err.response?.data?.error || 'Error al enviar el formulario'
    // También reseteamos el captcha en caso de error para que el usuario pueda reintentar
    if (captchaWidgetId.value !== null) {
      grecaptcha.reset(captchaWidgetId.value)
    }
  } finally {
    enviando.value = false
  }
}
</script>

<template>
  <div class="min-h-[90vh] flex items-center justify-center">
    <div class="relative p-10 bg-white rounded-2xl shadow-lg max-w-lg w-full">
      <div class="absolute inset-0 -z-10 transform rotate-6 bg-blue-500 rounded-2xl"></div>

      <h2 class="text-xl font-semibold text-gray-800">
        <span class="text-blue-600 font-bold">¿Necesitas ayuda?</span> Por favor completa el
        formulario.
      </h2>

      <form class="mt-5 space-y-4" @submit.prevent="enviarFormulario">
        <div>
          <label class="block font-medium text-gray-700">Nombre completo*</label>
          <input
            v-model="nombreCompleto"
            type="text"
            placeholder="Tu nombre"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Correo*</label>
          <input
            v-model="correo"
            type="email"
            placeholder="Tu correo"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Teléfono*</label>
          <input
            v-model="telefono"
            type="tel"
            placeholder="Tu número"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none"
            required
          />
        </div>

        <div>
          <label class="block font-medium text-gray-700">Mensaje*</label>
          <textarea
            v-model="mensaje"
            placeholder="Escribe tu mensaje"
            rows="4"
            class="w-full mt-1 p-3 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none"
            required
          ></textarea>
        </div>

        <div>
          <label class="flex items-center cursor-pointer">
            <input
              v-model="terminos"
              type="checkbox"
              class="form-checkbox h-5 w-5 text-blue-600 rounded border-gray-300 focus:ring-blue-500"
            />
            <span class="ml-2 text-gray-700">Acepto los términos y condiciones*</span>
          </label>
        </div>

        <!-- Contenedor del captcha con ID -->
        <div id="captcha-container" class="mt-4"></div>

        <button
          type="submit"
          :disabled="enviando"
          class="w-full py-3 text-white bg-blue-600 rounded-md hover:bg-blue-700 transition"
        >
          {{ enviando ? 'Enviando...' : 'Enviar mensaje' }}
        </button>

        <!-- Mensajes -->
        <p v-if="exito" class="text-blue-600 font-medium mt-2">¡Formulario enviado con éxito!</p>
        <p v-if="error" class="text-red-600 font-medium mt-2">{{ error }}</p>
      </form>
    </div>
  </div>
</template>
