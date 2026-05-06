<script setup>
import { ref, computed, onMounted } from 'vue'

const candidates = ref([])

// Load from localStorage
onMounted(() => {
  const saved = localStorage.getItem('candidates')
  if (saved) candidates.value = JSON.parse(saved)
})

// NEW candidate (inline row)
const newCandidate = ref({
  name: '',
  email: '',
  position: '',
  experience: '',
  skills: ''
})

// Add candidate
function addCandidate() {
  console.log("ADDING:", newCandidate.value)

  if (!newCandidate.value.name || !newCandidate.value.email) {
    alert("Name and Email required")
    return
  }

  const candidate = {
    ...newCandidate.value,
    status: 'New',
    match: '-',
    date: new Date().toLocaleDateString()
  }

  candidates.value.unshift(candidate)

  localStorage.setItem('candidates', JSON.stringify(candidates.value))

  // reset
  newCandidate.value = {
    name: '',
    email: '',
    position: '',
    experience: '',
    skills: ''
  }
}

// Search
const search = ref('')
const filtered = computed(() => {
  return candidates.value.filter(c =>
    `${c.name} ${c.email} ${c.skills}`
      .toLowerCase()
      .includes(search.value.toLowerCase())
  )
})

// Stats
const stats = computed(() => ({
  total: candidates.value.length,
  new: candidates.value.filter(c => c.status === 'New').length,
  shortlisted: candidates.value.filter(c => c.status === 'Shortlisted').length,
  interviewed: candidates.value.filter(c => c.status === 'Interviewed').length
}))
</script>

<template>
<div class="content">

  <!-- HEADER -->
  <div class="header">
    <h1>Candidate Database</h1>
    <p>View and manage all candidate applications</p>
  </div>

  <!-- STATS -->
  <div class="stats">
    <div class="card"><h2>{{ stats.total }}</h2><p>Total Candidates</p></div>
    <div class="card"><h2>{{ stats.new }}</h2><p>New Applications</p></div>
    <div class="card"><h2>{{ stats.shortlisted }}</h2><p>Shortlisted</p></div>
    <div class="card"><h2>{{ stats.interviewed }}</h2><p>Interviewed</p></div>
  </div>

  <!-- SEARCH -->
  <div class="toolbar">
    <input v-model="search" placeholder="Search candidates..." />
  </div>

  <!-- TABLE -->
  <div class="table">

    <div class="row header-row">
      <span>Candidate</span>
      <span>Position</span>
      <span>Experience</span>
      <span>Skills</span>
      <span>Status</span>
      <span>Date</span>
    </div>

    <div class="row add-row">
    <input v-model="newCandidate.name" placeholder="Name" />
    <input v-model="newCandidate.email" placeholder="Email" /> <!-- ADD THIS -->
    <input v-model="newCandidate.position" placeholder="Position" />
    <input v-model="newCandidate.experience" placeholder="Exp" />
    <input v-model="newCandidate.skills" placeholder="Skills" />
    <button @click="addCandidate">Add</button>
    </div>

    <!-- DATA ROWS -->
    <div
      v-for="c in filtered"
      :key="c.email"
      class="row"
    >
      <div>
        <strong>{{ c.name }}</strong>
        <p>{{ c.email }}</p>
      </div>

      <span>{{ c.position }}</span>
      <span>{{ c.experience }}</span>

      <div class="skills">
        <span v-for="s in c.skills.split(',')" :key="s">{{ s.trim() }}</span>
      </div>

      <span class="status" :class="c.status.toLowerCase()">
        {{ c.status }}
      </span>

      <span>{{ c.date }}</span>
    </div>

  </div>

</div>
</template>

<style scoped>
.header {
  margin-bottom: 20px;
}

.stats {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
}

.card {
  flex: 1;
  background: white;
  padding: 20px;
  border-radius: 12px;
}

.toolbar input {
  width: 100%;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
}

.table {
  background: white;
  border-radius: 12px;
  padding: 10px;
}

.row {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr 2fr 1fr 1fr;
  align-items: center;
  padding: 12px;
  border-bottom: 1px solid #eee;
}

.header-row {
  font-weight: bold;
}

.add-row input {
  padding: 6px;
  border-radius: 6px;
  border: 1px solid #ccc;
}

.add-row button {
  padding: 6px;
  border: none;
  background: #3b82f6;
  color: white;
  border-radius: 6px;
  cursor: pointer;
}

.skills span {
  background: #eee;
  padding: 4px 6px;
  border-radius: 5px;
  margin-right: 4px;
  font-size: 12px;
}

.status {
  padding: 4px 8px;
  border-radius: 10px;
  font-size: 12px;
}

.status.new { background: #dbeafe; }
.status.shortlisted { background: #dcfce7; }
.status.interviewed { background: #fde68a; }
</style>