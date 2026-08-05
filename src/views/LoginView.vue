<template>
  <div class="login-container">
    <form class="login-form login-card" @submit.prevent="handleLogin">
      <h1 class="login-title">Entrar</h1>

      <div 
        v-if="successMessage" 
        class="success-message"
      >
        {{ successMessage }}
      </div>

      <div
        v-if="errorMessage"
        class="error-message login-error"
      >
        {{ errorMessage }}
      </div>

      <div class="field login-field">
        <label
          for="email"
          class="login-label"
        >
          Email
        </label>

        <input
          id="email"
          v-model="email"
          type="email"
          placeholder="seu@email.com"
          required
          autocomplete="email"
          class="login-input"
        />
      </div>

      <div class="field login-field">
        <label
          for="password"
          class="login-label"
        >
          Senha
        </label>

        <input
          id="password"
          v-model="password"
          type="password"
          placeholder="••••••••"
          required
          autocomplete="current-password"
          class="login-input"
        />
      </div>

      <button
        type="submit"
        :disabled="loading"
        class="login-button"
      >
        {{ loading ? 'Entrando...' : 'Entrar' }}
      </button>
      <p class="register-link">
        Não possui uma conta?

        <RouterLink to="/register">
          Criar conta
        </RouterLink>
      </p>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { useAuthStore } from '@/stores/auth';
import { RouterLink } from 'vue-router'
import { useRoute } from 'vue-router'

const route = useRoute()
const router = useRouter();
const authStore = useAuthStore();

const email = ref('');
const password = ref('');
const loading = ref(false);
const errorMessage = ref('');
const successMessage = ref('');

if (route.query.registered === 'true') {
  successMessage.value = 'Conta criada com sucesso. Faça seu login.';
}

async function handleLogin() {
  loading.value = true;
  errorMessage.value = '';
  try {
    await authStore.login(email.value, password.value);
    router.push('/');
  } catch (err) {
    errorMessage.value =
      err.response?.data?.detail ??
      'Erro ao entrar. Verifique suas credenciais.';
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.login-container {
  min-height: calc(100vh - 80px);
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f5f5f5;
  padding: 24px;
  font-family: "Inter", sans-serif;
}

.login-form {
  width: 100%;
  max-width: 430px;
  background: white;
  padding: 36px;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, .08);
  border: 1px solid #ececec;
}

.login-form h1 {
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
  .login-form {
    padding: 28px 22px;
  }

  .login-form h1 {
    font-size: 1.7rem;
  }
}
</style>