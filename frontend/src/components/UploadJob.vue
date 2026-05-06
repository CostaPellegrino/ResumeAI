<script setup>
import { ref, onMounted } from 'vue'

const jobPostings = ref([])
const fileInput = ref(null)
const errorMessage = ref('')

// Load from localStorage
onMounted(() => {
  const saved = localStorage.getItem('jobPostings')
  if (saved) jobPostings.value = JSON.parse(saved)
})

// Save to localStorage
function saveToStorage() {
  localStorage.setItem('jobPostings', JSON.stringify(jobPostings.value))
}

// Handle file upload
function handleUpload(event) {
  const files = event.target.files

  const allowedTypes = [
    'application/pdf',
    'application/msword',
    'application/vnd.openxmlformats-officedocument.wordprocessingml.document'
  ]

  const maxSize = 5 * 1024 * 1024 // 5MB

  errorMessage.value = ''

  for (let file of files) {

    if (!allowedTypes.includes(file.type)) {
      errorMessage.value = `Invalid file type: ${file.name}`
      continue
    }

    if (file.size > maxSize) {
      errorMessage.value = `File too large: ${file.name} (Max 5MB)`
      continue
    }

    const newPosting = {
      name: file.name,
      size: (file.size / 1024).toFixed(0) + ' KB',
      date: new Date().toLocaleString()
    }

    jobPostings.value.unshift(newPosting)
  }

  saveToStorage()
  event.target.value = ''
}

function removePosting(index) {
  jobPostings.value.splice(index, 1)
  saveToStorage()
}
</script>

<template>
<div class="content">

<div v-if="errorMessage" class="error-box">
  <span class="error-icon">⚠️</span>
  <span class="error-text">{{ errorMessage }}</span>
</div>

  <div class="header">
    <h1>Upload Job Postings</h1>
    <p>Upload job descriptions for AI-powered candidate matching</p>
  </div>

  <div class="upload-box" @click="fileInput.click()">

    <input 
      type="file" 
      multiple 
      ref="fileInput" 
      @change="handleUpload" 
    />

    <p>
      Drop your job posting here, or 
      <span @click.stop="fileInput.click()">browse</span>
    </p>

    <small>Supports PDF, DOC, DOCX</small>

  </div>

  <div class="resume-list">
    <h3>Uploaded Job Postings ({{ jobPostings.length }})</h3>

    <div
      v-for="(p, index) in jobPostings"
      :key="p.name"
      class="resume-item"
    >
      <div>
        <strong>{{ p.name }}</strong>
        <p>{{ p.size }} • {{ p.date }}</p>
      </div>

      <div class="actions">
        <span class="check">✔</span>
        <button @click="removePosting(index)">✖</button>
      </div>
    </div>

  </div>

</div>
</template>

<style scoped>
.upload-box {
  border: 2px dashed #ccc;
  border-radius: 12px;
  padding: 40px;
  text-align: center;
  background: white;
  margin-bottom: 20px;
  cursor: pointer;
}

.upload-box input {
  display: none;
}

.upload-box span {
  color: #3b82f6;
  cursor: pointer;
}

.resume-list {
  background: white;
  padding: 20px;
  border-radius: 12px;
}

.resume-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-radius: 10px;
  background: #f9f9f9;
  margin-bottom: 10px;
}

.actions {
  display: flex;
  gap: 10px;
}

.check {
  color: green;
}

button {
  border: none;
  background: none;
  cursor: pointer;
}

.error-box {
  display: flex;
  align-items: center;
  gap: 10px;
  background: #fee2e2;
  color: #b91c1c;
  border: 1px solid #fecaca;
  padding: 12px 16px;
  border-radius: 10px;
  margin-bottom: 15px;
  font-size: 14px;
  animation: fadeIn 0.3s ease;
}

.error-icon {
  font-size: 16px;
}

.error-text {
  flex: 1;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>