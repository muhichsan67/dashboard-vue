<script setup>
import { ref } from 'vue'

// Menerima data dari parent component melalui props
defineProps({
  user: {
    type: Object,
    required: true,
  },
})

// PROBLEM 1: Local state untuk melacak apakah detail terbuka atau tertutup
const tampilDetail = ref(false)

function toggleDetail() {
  tampilDetail.value = !tampilDetail.value
}
</script>

<template>
  <li class="user-card">
    <div class="user-info">
      <strong>{{ user.name }}</strong>
      <span>{{ user.email }}</span>

      <!-- PROBLEM 1: Detail tambahan pengguna, muncul berdasarkan kondisi tampilDetail -->
      <div v-if="tampilDetail" class="user-details">
        <p>Telepon: {{ user.phone }}</p>
        <p>Perusahaan: {{ user.company.name }}</p>
        <p>Kota: {{ user.address.city }}</p>
      </div>
    </div>

    <!-- PROBLEM 1: Tombol untuk toggle detail dengan label dinamis -->
    <button @click="toggleDetail">
      {{ tampilDetail ? 'Sembunyikan' : 'Lihat Detail' }}
    </button>
  </li>
</template>

<style scoped>
.user-card {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 0.25rem;
  padding: 0.75rem 1rem;
  border-bottom: 1px solid #e0e0e0;
}
.user-info {
  display: flex;
  flex-direction: column;
}
.user-details {
  margin-top: 0.75rem;
  font-size: 0.9em;
  color: #333;
}
.user-details p {
  margin: 0.25rem 0;
}
button {
  padding: 0.4rem 0.8rem;
  cursor: pointer;
  background-color: white;
  border: 1px solid #0b4f6c;
  color: #0b4f6c;
  border-radius: 4px;
}
button:hover {
  background-color: #f0f8ff;
}
</style>
