<template>
  <q-card class="register-card">
    <q-card-section class="bg-primary text-white">
      <div class="text-h6">Crear Cuenta</div>
      <div class="text-caption">Completa el formulario para registrarte</div>
    </q-card-section>

    <q-card-section>
      <q-form @submit.prevent="handleRegister" class="q-gutter-md">
        <!-- Nombre -->
        <q-input
          v-model="form.firstName"
          label="Nombre *"
          outlined
          dense
          :rules="[val => !!val || 'El nombre es requerido']"
          @blur="$v.firstName.$touch"
        />

        <!-- Apellido -->
        <q-input
          v-model="form.lastName"
          label="Apellido"
          outlined
          dense
        />

        <!-- Email -->
        <q-input
          v-model="form.email"
          label="Email *"
          type="email"
          outlined
          dense
          :rules="[
            val => !!val || 'El email es requerido',
            val => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(val) || 'Email no válido'
          ]"
          @blur="$v.email.$touch"
        />

        <!-- Contraseña -->
        <q-input
          v-model="form.password"
          label="Contraseña *"
          :type="showPassword ? 'text' : 'password'"
          outlined
          dense
          :rules="[
            val => !!val || 'La contraseña es requerida',
            val => val?.length >= 6 || 'La contraseña debe tener al menos 6 caracteres'
          ]"
          @blur="$v.password.$touch"
        >
          <template v-slot:append>
            <q-icon
              :name="showPassword ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="showPassword = !showPassword"
            />
          </template>
        </q-input>

        <!-- Confirmar Contraseña -->
        <q-input
          v-model="confirmPassword"
          label="Confirmar Contraseña *"
          :type="showConfirmPassword ? 'text' : 'password'"
          outlined
          dense
          :rules="[
            val => !!val || 'Confirma tu contraseña',
            val => val === form.password || 'Las contraseñas no coinciden'
          ]"
          @blur="$v.confirmPassword.$touch"
        >
          <template v-slot:append>
            <q-icon
              :name="showConfirmPassword ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="showConfirmPassword = !showConfirmPassword"
            />
          </template>
        </q-input>

        <!-- Fecha de Nacimiento -->
        <q-input
          v-model="form.dateOfBirth"
          label="Fecha de Nacimiento"
          type="date"
          outlined
          dense
        />

        <!-- País -->
        <q-input
          v-model="form.country"
          label="País"
          outlined
          dense
        />

        <!-- Dirección -->
        <q-input
          v-model="form.address"
          label="Dirección"
          outlined
          dense
          type="textarea"
          rows="3"
        />

        

        <!-- Términos y Condiciones -->
        <q-checkbox
          v-model="acceptTerms"
          label="Aceptar términos y condiciones *"
          :rules="[val => !!val || 'Debes aceptar los términos y condiciones']"
        />

        <!-- Botón Enviar -->
        <q-btn
          unelevated
          label="Registrarse"
          type="submit"
          color="primary"
          class="full-width"
          :loading="loading"
          :disable="!acceptTerms"
        />

        <!-- Enlace a Login -->
        <div class="text-center q-mt-md">
          <span class="text-caption"
            >¿Ya tienes cuenta?
            <router-link to="/login" class="text-primary">Inicia sesión aquí</router-link>
          </span>
        </div>
      </q-form>
    </q-card-section>
  </q-card>
</template>

<style scoped lang="scss">
.register-card {
  max-width: 500px;
  margin: 0 auto;
}
</style>

<script setup>
import { ref, reactive } from 'vue'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'
import { api } from 'boot/axios'

const $q = useQuasar()
const router = useRouter()

// Estado del formulario
const form = reactive({
  firstName: '',
  lastName: '',
  email: '',
  password: '',
  dateOfBirth: '',
  country: '',
  address: '',
  type: null
})

// Variables de control
const confirmPassword = ref('')
const showPassword = ref(false)
const showConfirmPassword = ref(false)
const acceptTerms = ref(false)
const loading = ref(false)



// Validación básica (puedes expandir con Vuelidate si lo prefieres)
const $v = {
  firstName: { $touch: () => {} },
  email: { $touch: () => {} },
  password: { $touch: () => {} },
  confirmPassword: { $touch: () => {} }
}

// Función para manejar el registro
const handleRegister = async () => {
  // Validar que las contraseñas coincidan
  if (form.password !== confirmPassword.value) {
    $q.notify({
      type: 'negative',
      message: 'Las contraseñas no coinciden',
      position: 'top'
    })
    return
  }

  // Validar que se acepten los términos
  if (!acceptTerms.value) {
    $q.notify({
      type: 'negative',
      message: 'Debes aceptar los términos y condiciones',
      position: 'top'
    })
    return
  }

  loading.value = true

  try {
    // Preparar datos para enviar
    const payload = {
      firstName: form.firstName.trim(),
      lastName: form.lastName.trim() || null,
      email: form.email.trim(),
      password: form.password,
      dateOfBirth: form.dateOfBirth || null,
      country: form.country.trim() || null,
      address: form.address.trim() || null,
      type: form.type || null
    }

    // Realizar petición al servidor
    await api.post('/api/user/signup', payload)

    // Mostrar mensaje de éxito
    $q.notify({
      type: 'positive',
      message: 'Registro exitoso. Por favor inicia sesión.',
      position: 'top'
    })

    // Limpiar formulario
    resetForm()

    // Redirigir a login después de 1.5 segundos
    setTimeout(() => {
      router.push('/login')
    }, 1500)
  } catch (error) {
    // Manejar errores
    const errorMessage = error.response?.data?.message || 
                        error.response?.data || 
                        error.message || 
                        'Error en el registro'
    
    $q.notify({
      type: 'negative',
      message: `Error: ${errorMessage}`,
      position: 'top',
      timeout: 3000
    })

    console.error('Error en registro:', error)
  } finally {
    loading.value = false
  }
}

// Función para limpiar el formulario
const resetForm = () => {
  form.firstName = ''
  form.lastName = ''
  form.email = ''
  form.password = ''
  form.dateOfBirth = ''
  form.country = ''
  form.address = ''
  form.type = null
  confirmPassword.value = ''
  acceptTerms.value = false
  showPassword.value = false
  showConfirmPassword.value = false
}
</script>