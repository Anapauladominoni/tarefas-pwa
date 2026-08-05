<template>
  <div class="register-container">
    <form class="register-form" @submit.prevent="handleRegister">
      <h1>Criar conta</h1>

      <div v-if="errorMessage" class="error-message">
        {{ errorMessage }}
      </div>

      <div class="field">
        <label>Email</label>
        <input
          v-model="email"
          type="email"
          required
          placeholder="seu@email.com"
        />
      </div>

      <div class="field">
        <label>Senha</label>
        <input
          v-model="password"
          type="password"
          required
        />
      </div>

      <div class="field">
        <label>Confirmar senha</label>
        <input
          v-model="confirmPassword"
          type="password"
          required
        />
      </div>

      <button type="submit" :disabled="loading">
        {{ loading ? 'Criando conta...' : 'Cadastrar' }}
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import authApi from '@/api/authApi'

const router = useRouter()

const email = ref('')
const password = ref('')
const confirmPassword = ref('')

const loading = ref(false)
const errorMessage = ref('')

async function handleRegister() {
  if (password.value !== confirmPassword.value) {
    errorMessage.value = 'As senhas não coincidem.'
    return
  }
  loading.value = true
  errorMessage.value = ''
  try {
    await authApi.register(email.value, password.value)
    router.push('/register?registered=true')
  } catch (err) {
    errorMessage.value = 
      err.response?.data?.detail ??
      'Erro ao cadastrar usuário.'
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.register-container {
  min-height: calc(100vh - 80px);
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f5f5f5;
  padding: 24px;
  font-family: "Inter", sans-serif;
}

.register-form {
  width: 100%;
  max-width: 430px;
  background: white;
  padding: 36px;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, .08);
  border: 1px solid #ececec;
}

.register-form h1 {
  margin: 0;
  color: #4a90d9;
  font-size: 2rem;
  text-align: center;
}

.subtitle {
  text-align: center;
  color: #777;
  font-size: .95rem;
  margin: 10px 0 28px;
}

.field {
  display: flex;
  flex-direction: column;
  margin-bottom: 18px;
}

.field label {
  margin-bottom: 8px;
  font-size: .9rem;
  color: #555;
  font-weight: 500;
}

.field input {
  padding: 13px 14px;
  border: 2px solid #ddd;
  border-radius: 8px;
  outline: none;
  font-size: 1rem;
  transition: .2s;
}

.field input:focus {
  border-color: #4a90d9;
}

button {
  width: 100%;
  margin-top: 8px;
  padding: 14px;
  background: #4a90d9;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: .2s;
}

button:hover:not(:disabled) {
  background: #357abd;
}

button:disabled {
  opacity: .6;
  cursor: not-allowed;
}

.error-message {
  margin-bottom: 18px;
  padding: 12px;
  background: #fdecec;
  color: #d64545;
  border-radius: 8px;
  border: 1px solid #f3bcbc;
  font-size: .9rem;
}

@media (max-width: 500px) {
  .register-form {
    padding: 28px 22px;
  }

  .register-form h1 {
    font-size: 1.7rem;
  }
}
</style>