<script setup>
import { ref, computed, watch } from 'vue'
import UserCard from './components/UserCard.vue'

const judul = 'Dashboard Vue — Pertemuan 5'
const users = ref([])
const keadaan = ref('idle') // idle | loading | empty | error | success

async function muatPengguna() {
  keadaan.value = 'loading'
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    if (!response.ok) {
      throw new Error('Status HTTP: ' + response.status)
    }
    const data = await response.json()
    if (data.length === 0) {
      keadaan.value = 'empty'
      return
    }
    users.value = data
    keadaan.value = 'success'
  } catch (error) {
    console.error('Gagal memuat data:', error)
    keadaan.value = 'error'
  }
}

// Fitur Pencarian (Sesi 5)
const queryPencarian = ref('')
const riwayatPencarian = ref([])

const penggunaTersaring = computed(() => {
  const q = queryPencarian.value.toLowerCase().trim()
  if (!q) return users.value
  return users.value.filter((user) => (user.name.toLowerCase().includes(q) || user.email.toLowerCase().includes(q)))
})

watch(queryPencarian, (nilaiBaru) => {
  if (nilaiBaru.trim() !== '') {
    riwayatPencarian.value.push(nilaiBaru)
  }
})

// PROBLEM 2: State untuk menyimpan arah pengurutan yang aktif
const arahUrutan = ref('') // Boleh berisi '', 'asc', atau 'desc'

// PROBLEM 2: Chained computed yang bersumber pada penggunaTersaring
const penggunaTerurut = computed(() => {
  // Gunakan spread operator [...array] untuk menduplikasi array sebelum disort
  const hasil = [...penggunaTersaring.value]

  if (arahUrutan.value === 'asc') {
    hasil.sort((a, b) => a.name.localeCompare(b.name))
  } else if (arahUrutan.value === 'desc') {
    hasil.sort((a, b) => b.name.localeCompare(a.name))
  }

  return hasil
})

function setUrutan(arah) {
  arahUrutan.value = arah
}
</script>

<template>
  <header class="app-header">
    <h1>{{ judul }}</h1>
  </header>

  <main class="app-main">
    <div class="controls-container">
      <button class="btn-primary" @click="muatPengguna">Muat Pengguna</button>
      <input v-model="queryPencarian" type="text" placeholder="Cari username..." />

      <!-- PROBLEM 2: Tombol pengurutan dengan class binding dinamis -->
      <button :class="['btn-sort', { active: arahUrutan === 'asc' }]" @click="setUrutan('asc')">
        Urutkan A-Z
      </button>

      <button :class="['btn-sort', { active: arahUrutan === 'desc' }]" @click="setUrutan('desc')">
        Urutkan Z-A
      </button>
    </div>

    <!-- State UI (Loading, Empty, Error) (Sesi 5) -->
    <p v-if="keadaan === 'loading'">Memuat data...</p>
    <p v-else-if="keadaan === 'empty'">Tidak ada pengguna ditemukan.</p>
    <p v-else-if="keadaan === 'error'">Gagal memuat data. Coba lagi.</p>

    <ul v-else-if="keadaan === 'success'" class="user-list">
      <!-- PROBLEM 2: Render daftar dari penggunaTerurut (chained computed), BUKAN lagi penggunaTersaring -->
      <UserCard v-for="user in penggunaTerurut" :key="user.id" :user="user" />
    </ul>
  </main>
</template>

<style scoped>
.app-header {
  padding: 1.5rem;
  background: #0b4f6c;
  color: white;
}
.app-main {
  padding: 1.5rem;
}
.controls-container {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
  align-items: center;
}
input {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}
.btn-primary {
  padding: 0.5rem 1rem;
  background: #0b4f6c;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
/* PROBLEM 2: Style dasar untuk tombol urut */
.btn-sort {
  padding: 0.5rem 1rem;
  background: #fff;
  border: 1px solid #ccc;
  color: #333;
  border-radius: 4px;
  cursor: pointer;
}
/* PROBLEM 2: Visual aktif ketika status sort sesuai */
.btn-sort.active {
  background: #0b4f6c;
  color: white;
  border-color: #0b4f6c;
}
.user-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
</style>
