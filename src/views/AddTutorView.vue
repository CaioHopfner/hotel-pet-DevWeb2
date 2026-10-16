<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const novoTutor = ref({
  id: '',
  nome: '',
  cpf: '',
  telefone: '',
  email: '',
  dataNascimento: '',
  rua: '',
  numero: '',
  bairro: '',
  cidade: '',
  estado: '',
  cep: '',
});
const erro = ref('');

async function salvarTutor() {
  try {
    const resposta = await fetch(`${API_URL}/tutores`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(novoTutor.value),
    });

    if (!resposta.ok) {
      throw new Error('Não foi possível salvar o tutor.');
    }

    await router.push('/tutores');
  } catch (error) {
    console.error('Erro ao salvar tutor:', error);
    erro.value = 'Não foi possível salvar o tutor. Tente novamente.';
  }
}

function cancelarCadastro() {
  router.push('/tutores');
}
</script>

<template>
  <div class="tutor-page">
    <header class="mb-4">
      <h1 class="page-title">Cadastrar Tutor</h1>
      <p class="page-subtitle">
        Preencha os dados para cadastrar um novo tutor.
      </p>
    </header>

    <div
      v-if="erro"
      class="alert alert-danger"
      role="alert"
    >
      {{ erro }}
    </div>

    <section class="form-section">
      <h2 class="section-title">Dados do Tutor</h2>

      <form
        @submit.prevent="salvarTutor"
        class="tutor-form"
      >
        <div class="row g-4">
          <div class="col-md-6">
            <label
              for="idTutor"
              class="field-label"
              >ID</label
            >
            <input
              id="idTutor"
              v-model="novoTutor.id"
              type="text"
              class="form-control custom-input"
              disabled
              placeholder="Gerado automaticamente"
            />
          </div>

          <div class="col-md-6">
            <label
              for="nome"
              class="field-label"
              >Nome Completo</label
            >
            <input
              id="nome"
              v-model="novoTutor.nome"
              type="text"
              class="form-control custom-input"
              required
              placeholder="Digite o nome completo"
            />
          </div>

          <div class="col-md-6">
            <label
              for="cpf"
              class="field-label"
              >CPF / CNPJ</label
            >
            <input
              id="cpf"
              v-model="novoTutor.cpf"
              type="text"
              class="form-control custom-input"
              required
              placeholder="Digite o CPF ou CNPJ"
            />
          </div>

          <div class="col-md-6">
            <label
              for="telefone"
              class="field-label"
              >Telefone / Celular / WhatsApp</label
            >
            <input
              id="telefone"
              v-model="novoTutor.telefone"
              type="tel"
              class="form-control custom-input"
              required
              placeholder="(00) 00000-0000"
            />
          </div>

          <div class="col-md-6">
            <label
              for="email"
              class="field-label"
              >E-mail</label
            >
            <input
              id="email"
              v-model="novoTutor.email"
              type="email"
              class="form-control custom-input"
              required
              placeholder="nome@example.com"
            />
          </div>

          <div class="col-md-6">
            <label
              for="dataNascimento"
              class="field-label"
              >Data de Nascimento</label
            >
            <input
              id="dataNascimento"
              v-model="novoTutor.dataNascimento"
              type="date"
              class="form-control custom-input"
              required
            />
          </div>
        </div>

        <h2 class="section-title mt-5">Endereço</h2>

        <div class="row g-4">
          <div class="col-md-6">
            <label
              for="rua"
              class="field-label"
              >Rua</label
            >
            <input
              id="rua"
              v-model="novoTutor.rua"
              type="text"
              class="form-control custom-input"
              placeholder="Digite o nome da rua"
              required
            />
          </div>

          <div class="col-md-6">
            <label
              for="numero"
              class="field-label"
              >Número</label
            >
            <input
              id="numero"
              v-model="novoTutor.numero"
              type="text"
              class="form-control custom-input"
              placeholder="Número"
              required
            />
          </div>

          <div class="col-md-6">
            <label
              for="bairro"
              class="field-label"
              >Bairro</label
            >
            <input
              id="bairro"
              v-model="novoTutor.bairro"
              type="text"
              class="form-control custom-input"
              placeholder="Digite o bairro"
              required
            />
          </div>

          <div class="col-md-6">
            <label
              for="cidade"
              class="field-label"
              >Cidade</label
            >
            <input
              id="cidade"
              v-model="novoTutor.cidade"
              type="text"
              class="form-control custom-input"
              placeholder="Digite a cidade"
              required
            />
          </div>

          <div class="col-md-6">
            <label
              for="estado"
              class="field-label"
              >Estado</label
            >
            <select
              id="estado"
              v-model="novoTutor.estado"
              class="form-select custom-input"
              required
            >
              <option value="">Selecione</option>
              <option value="SP">SP</option>
              <option value="RJ">RJ</option>
              <option value="MG">MG</option>
              <option value="PR">PR</option>
              <option value="SC">SC</option>
              <option value="RS">RS</option>
            </select>
          </div>

          <div class="col-md-6">
            <label
              for="cep"
              class="field-label"
              >CEP</label
            >
            <input
              id="cep"
              v-model="novoTutor.cep"
              type="text"
              class="form-control custom-input"
              placeholder="00000-000"
              required
            />
          </div>
        </div>

        <div class="d-flex justify-content-end gap-3 mt-4 pt-2">
          <button
            type="button"
            class="btn btn-cancel"
            @click="cancelarCadastro"
          >
            Cancelar
          </button>
          <button
            type="submit"
            class="btn btn-save"
          >
            Cadastrar Tutor
          </button>
        </div>
      </form>
    </section>
  </div>
</template>

<style scoped>
.tutor-page {
  padding: 0.25rem 0.5rem 0;
}

.page-title {
  margin: 0;
  font-size: clamp(2.4rem, 3vw, 3.2rem);
  font-weight: 800;
  color: #1f2937;
  letter-spacing: -0.03em;
}

.page-subtitle {
  margin: 0.5rem 0 0;
  font-size: 1.15rem;
  color: #6b7280;
}

.form-section {
  margin-top: 1.25rem;
}

.section-title {
  margin: 0 0 1.25rem;
  padding-bottom: 0.8rem;
  border-bottom: 1px solid #e5e7eb;
  font-size: 2rem;
  font-weight: 700;
  color: #1f2937;
}

.tutor-form {
  width: 100%;
}

.field-label {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 1.05rem;
  font-weight: 600;
  color: #1f2937;
}

.custom-input,
.form-select.custom-input {
  height: 52px;
  border: 1px solid #d7dce3;
  border-radius: 0.5rem;
  background: #f3f5f7;
  color: #2b2f36;
  box-shadow: none;
  transition: all 0.2s ease;
}

.custom-input::placeholder {
  color: #8a919d;
}

.custom-input:focus,
.form-select.custom-input:focus {
  border-color: #7ab6ff;
  background: #ffffff;
  box-shadow: 0 0 0 0.2rem rgba(58, 123, 255, 0.12);
}

.btn-cancel {
  min-width: 120px;
  border-radius: 0.5rem;
  background: #e9ecef;
  color: #1f2937;
  border: 1px solid #d9dde3;
  font-weight: 600;
  padding: 0.75rem 1.25rem;
}

.btn-save {
  min-width: 170px;
  border-radius: 0.5rem;
  background: #0d6efd;
  color: #ffffff;
  border: none;
  font-weight: 700;
  padding: 0.8rem 1.4rem;
}

.btn-save:hover,
.btn-save:focus {
  background: #0b5ed7;
}

@media (max-width: 768px) {
  .page-title {
    font-size: 2.2rem;
  }

  .section-title {
    font-size: 1.6rem;
  }
}
</style>
