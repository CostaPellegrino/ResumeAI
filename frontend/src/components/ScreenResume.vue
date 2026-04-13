<script setup>
import { ref, computed } from 'vue'

// Props
const props = defineProps(['jobs'])

// Sample candidates for now
const candidates = ref([
  {
    name: "Sarah Johnson",
    email: "sarah.johnson@email.com",
    skills: "React, TypeScript, Node.js",
    experience: "5 years",
    education: "BS Computer Science"
  },
  {
    name: "Michael Chen",
    email: "michael.chen@email.com",
    skills: "Python, Machine Learning, React",
    experience: "3 years",
    education: "MS Data Science"
  },
  {
    name: "Emily Rodriguez",
    email: "emily.r@email.com",
    skills: "JavaScript, Vue, CSS",
    experience: "4 years",
    education: "BS Software Engineering"
  }
])

const selectedJob = ref(null)
const selectedCandidate = ref(null)
const screenedResult = ref(null)
const skillFilter = ref('')

function parseSkills(skills) {
  return skills
    .toLowerCase()
    .split(',')
    .map(s => s.trim())
}

function getMatchData(candidate) {
  if (!selectedJob.value) return null

  const jobSkills = parseSkills(selectedJob.value.skills)
  const candidateSkills = parseSkills(candidate.skills)

  const matched = []
  const missing = []

  jobSkills.forEach(skill => {
    if (candidateSkills.includes(skill)) {
      matched.push(skill)
    } else {
      missing.push(skill)
    }
  })

  const score = Math.round((matched.length / jobSkills.length) * 100)

  return { score, matched, missing }
}

// Score preview
function matchScore(candidate) {
  const data = getMatchData(candidate)
  return data ? data.score : 0
}

// Sort candidates
const filteredCandidates = computed(() => {
  let list = candidates.value

  // Apply skill filter
  if (skillFilter.value.trim()) {
    const search = skillFilter.value.toLowerCase()

    list = list.filter(c =>
      c.skills.toLowerCase().includes(search)
    )
  }

  // Apply ranking if job selected
  if (selectedJob.value) {
    return [...list].sort(
      (a, b) => matchScore(b) - matchScore(a)
    )
  }

  return list
})

// Select candidate
function selectCandidate(c) {
  selectedCandidate.value = c
  screenedResult.value = null // reset old result
}

// Manual screen trigger
function screenCandidate() {
  if (!selectedCandidate.value || !selectedJob.value) return
  screenedResult.value = getMatchData(selectedCandidate.value)
}
</script>

<template>
<div class="content">

  <div class="header">
    <h1>Screen Resume</h1>
    <p>AI-powered resume screening and candidate evaluation</p>
  </div>

  <div class="job-select">
    <h3>Select Job Posting</h3>
    <select v-model="selectedJob">
      <option disabled value="">Choose a job...</option>
      <option v-for="job in props.jobs" :key="job.title" :value="job">
        {{ job.title }}
      </option>
    </select>
  </div>

    <div class="filter-box">
      <input 
        v-model="skillFilter" 
        placeholder="Filter by skill (e.g. React, Python)" 
      />
    </div>

  <div class="screen-container">

    <div class="candidates">
      <h3>Candidates ({{ filteredCandidates.length }})</h3>

      <div
        v-for="c in filteredCandidates"
        :key="c.email"
        class="candidate-card"
        @click="selectCandidate(c)"
      >
        <div>
          <strong>{{ c.name }}</strong>
          <p>{{ c.email }}</p>

          <div class="skills">
            <span v-for="s in parseSkills(c.skills)" :key="s">
              {{ s }}
            </span>
          </div>
        </div>

        <div class="score">
          {{ matchScore(c) }}%
        </div>
      </div>
    </div>

    <div class="profile" v-if="selectedCandidate">

      <h3>Candidate Profile</h3>

      <p><strong>Name:</strong> {{ selectedCandidate.name }}</p>
      <p><strong>Email:</strong> {{ selectedCandidate.email }}</p>
      <p><strong>Experience:</strong> {{ selectedCandidate.experience }}</p>
      <p><strong>Education:</strong> {{ selectedCandidate.education }}</p>

      <div class="skills">
        <span v-for="s in parseSkills(selectedCandidate.skills)" :key="s">
          {{ s }}
        </span>
      </div>

      <button class="screen-btn" @click="screenCandidate">
        {{ screenedResult ? "Re-Screen Resume" : "Screen Resume" }}
      </button>

      <div v-if="screenedResult" class="match-breakdown">
        <h3>Match Score: {{ screenedResult.score }}%</h3>

        <p>
          <strong>✅ Matched:</strong>
          {{ screenedResult.matched.join(', ') || 'None' }}
        </p>

        <p>
          <strong>❌ Missing:</strong>
          {{ screenedResult.missing.join(', ') || 'None' }}
        </p>
      </div>

    </div>

  </div>

</div>
</template>

<style scoped>
.job-select {
  background: white;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
}

select {
  width: 100%;
  padding: 10px;
  border-radius: 8px;
}

.screen-container {
  display: flex;
  gap: 20px;
}

.candidates {
  flex: 1;
  background: white;
  padding: 20px;
  border-radius: 12px;
}

.candidate-card {
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 15px;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  cursor: pointer;
}

.candidate-card:hover {
  border-color: #4caf50;
}

.skills span {
  background: #eee;
  padding: 5px 8px;
  border-radius: 6px;
  margin-right: 5px;
  font-size: 12px;
}

.score {
  font-weight: bold;
}

.profile {
  width: 320px;
  background: white;
  padding: 20px;
  border-radius: 12px;
}

.match-breakdown {
  margin-top: 20px;
}

.screen-btn {
  margin-top: 20px;
  width: 100%;
  padding: 10px;
  border: none;
  background: #4caf50;
  color: white;
  border-radius: 8px;
  cursor: pointer;
}

.screen-btn:hover {
  background: #45a049;
}

.filter-box {
  margin-bottom: 15px;
}

.filter-box input {
  width: 100%;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #ccc;
}
</style>