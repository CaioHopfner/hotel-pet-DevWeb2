<script setup>
import { onMounted, ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const router = useRouter();

const API_URL = 'http://localhost:3000';
const tutor = ref({});
const pets = ref([]);
const carregando = ref(true);

function mascararCpf(cpf) {
  if (!cpf) return '***.***.***-**';
  return '***.***.***-**';
}

function formatarData(data) {
  if (!data) return '';
  const dataObj = new Date(data);
  if (Number.isNaN(dataObj.getTime())) return data;

  return new Intl.DateTimeFormat('pt-BR').format(dataObj);
}

function formatarCep(cep) {
  if (!cep) return '';
  const cepNumerico = cep.replace(/\D/g, '').slice(0, 8);
  if (cepNumerico.length !== 8) return cep;
  return `${cepNumerico.slice(0, 5)}-${cepNumerico.slice(5)}`;
}

async function carregarDados() {
  try {
    carregando.value = true;

    const respostaTutor = await fetch(`${API_URL}/tutores/${route.params.id}`);
    if (!respostaTutor.ok) {
      throw new Error('Tutor não encontrado.');
    }

    tutor.value = await respostaTutor.json();

    const respostaPets = await fetch(`${API_URL}/pets`);
    if (!respostaPets.ok) {
      throw new Error('Não foi possível carregar os pets.');
    }

    const petsCadastrados = await respostaPets.json();
    pets.value = petsCadastrados.filter(
      (pet) => String(pet.tutorId) === String(tutor.value.id),
    );
  } catch (error) {
    console.error('Erro ao carregar dados do tutor:', error);
    router.push('/tutores');
  } finally {
    carregando.value = false;
  }
}

async function removerTutor() {
  if (
    !window.confirm(`Deseja realmente remover o tutor ${tutor.value.nome}?`)
  ) {
    return;
  }

  try {
    const resposta = await fetch(`${API_URL}/tutores/${tutor.value.id}`, {
      method: 'DELETE',
    });

    if (!resposta.ok) {
      throw new Error('Não foi possível remover o tutor.');
    }

    router.push('/tutores');
  } catch (error) {
    console.error('Erro ao remover tutor:', error);
    window.alert('Não foi possível remover o tutor.');
  }
}

onMounted(carregarDados);
</script>

<template>
  <div
    v-if="carregando"
    class="text-center py-5"
  >
    <div
      class="spinner-border text-primary"
      role="status"
    >
      <span class="visually-hidden">Carregando...</span>
    </div>
  </div>

  <div
    v-else
    class="tutor-details-page"
  >
    <header class="tutor-header">
      <div>
        <h1 class="page-title">Detalhes do Tutor</h1>
        <p class="page-subtitle">
          Visualize os dados do tutor e seus pets vinculados.
        </p>
      </div>

      <button
        type="button"
        class="btn btn-outline-primary back-button"
        @click="router.push('/tutores')"
      >
        ← Voltar para Tutores
      </button>
    </header>

    <section class="data-card">
      <h2 class="section-title">Dados do Tutor</h2>

      <div class="info-grid">
        <div class="info-item">
          <span class="label">ID</span>
          <strong>{{ tutor.id }}</strong>
        </div>
        <div class="info-item">
          <span class="label">Nome Completo</span>
          <strong>{{ tutor.nome }}</strong>
        </div>
        <div class="info-item">
          <span class="label">CPF / CNPJ</span>
          <strong>{{ mascararCpf(tutor.cpf) }}</strong>
        </div>

        <div class="info-item">
          <span class="label">Telefone / Celular / WhatsApp</span>
          <strong>{{ tutor.telefone }}</strong>
        </div>
        <div class="info-item">
          <span class="label">E-mail</span>
          <strong>{{ tutor.email }}</strong>
        </div>
        <div class="info-item">
          <span class="label">Data de Nascimento</span>
          <strong>{{ formatarData(tutor.dataNascimento) }}</strong>
        </div>
      </div>
    </section>

    <section class="data-card">
      <h2 class="section-title">Endereço</h2>

      <div class="info-grid">
        <div class="info-item">
          <span class="label">Rua</span>
          <strong>{{ tutor.rua }}</strong>
        </div>
        <div class="info-item">
          <span class="label">Número</span>
          <strong>{{ tutor.numero }}</strong>
        </div>
        <div class="info-item">
          <span class="label">Bairro</span>
          <strong>{{ tutor.bairro }}</strong>
        </div>

        <div class="info-item">
          <span class="label">Cidade</span>
          <strong>{{ tutor.cidade }}</strong>
        </div>
        <div class="info-item">
          <span class="label">Estado</span>
          <strong>{{ tutor.estado }}</strong>
        </div>
        <div class="info-item">
          <span class="label">CEP</span>
          <strong>{{ formatarCep(tutor.cep) }}</strong>
        </div>
      </div>
    </section>

    <section class="pets-card">
      <div class="pets-header">
        <h2 class="section-title inline-title">Pets vinculados</h2>
        <span class="badge-count"
          >{{ pets.length }} {{ pets.length === 1 ? 'pet' : 'pets' }}</span
        >
      </div>

      <table class="table table-striped table-hover mt-3">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nome</th>
            <th>Espécie</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="pet in pets"
            :key="pet.id"
          >
            <td>{{ pet.id }}</td>
            <td>{{ pet.nome }}</td>
            <td>{{ pet.especie }}</td>
            <td>
              <button
                type="button"
                class="btn btn-outline-primary btn-sm"
                @click="router.push(`/pets/${pet.id}`)"
              >
                Visualizar Pet
              </button>
            </td>
          </tr>
          <tr v-if="pets.length === 0">
            <td
              colspan="4"
              class="text-center text-body-secondary"
            >
              Nenhum pet vinculado a este tutor.
            </td>
          </tr>
        </tbody>
      </table>
    </section>

    <div class="remove-area">
      <button
        type="button"
        class="btn btn-outline-danger remove-button"
        @click="removerTutor"
      >
        Remover Tutor
      </button>
    </div>
  </div>
</template>

<style scoped>
.tutor-details-page {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.tutor-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 1rem;
}

.page-title {
  margin: 0;
  font-size: clamp(2.3rem, 3vw, 3.5rem);
  font-weight: 800;
  color: #1f2937;
  letter-spacing: -0.03em;
}

.page-subtitle {
  margin: 0.5rem 0 0;
  color: #6b7280;
  font-size: 1.1rem;
}

.back-button {
  border-radius: 0.6rem;
  border: 1px solid #0d6efd;
  color: #0d6efd;
  font-weight: 600;
  padding: 0.8rem 1.2rem;
}

.data-card,
.pets-card {
  background: #f7f7f7;
  border: 1px solid #dfe3e8;
  border-radius: 0.8rem;
  overflow: hidden;
}

.section-title {
  margin: 0;
  padding: 1rem 1.25rem;
  background: rgba(216, 220, 226, 0.65);
  font-size: 2rem;
  font-weight: 700;
  color: #1f2937;
}

.inline-title {
  padding: 0;
  background: transparent;
  font-size: 2rem;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1.25rem 1.5rem;
  padding: 1.25rem;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
  min-height: 68px;
}

.label {
  font-size: 0.9rem;
  color: #4b5563;
  font-weight: 600;
}

.info-item strong {
  font-size: 1.08rem;
  color: #111827;
  font-weight: 600;
}

.pets-card {
  padding: 1.25rem;
}

.pets-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 0.5rem;
}

.badge-count {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 68px;
  padding: 0.35rem 0.8rem;
  border-radius: 999px;
  background: #0d6efd;
  color: white;
  font-weight: 700;
  font-size: 0.9rem;
}

.remove-area {
  display: flex;
  justify-content: flex-start;
}

.remove-button {
  border: 2px solid #dc3545;
  color: #dc3545;
  font-weight: 700;
  border-radius: 0.6rem;
  padding: 0.75rem 1.5rem;
}

@media (max-width: 767px) {
  .tutor-header,
  .pets-header {
    flex-direction: column;
    align-items: stretch;
  }

  .info-grid {
    grid-template-columns: 1fr;
  }
}
</style>
