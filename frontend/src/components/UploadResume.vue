<script setup>
import { ref, onMounted } from 'vue'

const resumes = ref([])
const fileInput = ref(null)
const errorMessage = ref('')

// Load from localStorage
onMounted(() => {
  const saved = localStorage.getItem('resumes')
  if (saved) resumes.value = JSON.parse(saved)
})

// Save to localStorage
function saveToStorage() {
  localStorage.setItem('resumes', JSON.stringify(resumes.value))
}

// Handle file upload
function handleUpload(event) {
  const files = event.target.files

  // Allowed file types
  const allowedTypes = [
    'application/pdf',
    'application/msword',
    'application/vnd.openxmlformats-officedocument.wordprocessingml.document'
  ]

  const maxSize = 5 * 1024 * 1024 // 5MB

  errorMessage.value = '' // reset error

  for (let file of files) {

    // Invalid file type
    if (!allowedTypes.includes(file.type)) {
      errorMessage.value = `Invalid file type: ${file.name}`
      continue
    }

    // File too large
    if (file.size > maxSize) {
      errorMessage.value = `File too large: ${file.name} (Max 5MB)`
      continue
    }

    // Valid file
    const newResume = {
      name: file.name,
      size: (file.size / 1024).toFixed(0) + ' KB',
      date: new Date().toLocaleString()
    }

    resumes.value.unshift(newResume)
  }

  saveToStorage()

  // Reset input so same file can be uploaded again
  event.target.value = ''
}


function removeResume(index) {
  resumes.value.splice(index, 1)
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
    <h1>Upload Resume</h1>
    <p>Upload candidate resumes for AI-powered screening</p>
  </div>

    <div class="upload-box" @click="fileInput.click()">

        <input 
            type="file" 
            multiple 
            ref="fileInput" 
            @change="handleUpload" 
        />

        <p>
            Drop your resume here, or 
            <span @click.stop="fileInput.click()">browse</span>
        </p>

        <small>Supports PDF, DOC, DOCX</small>

    </div>

  <div class="resume-list">
    <h3>Uploaded Resumes ({{ resumes.length }})</h3>

    <div
      v-for="(r, index) in resumes"
      :key="r.name"
      class="resume-item"
    >
      <div>
        <strong>{{ r.name }}</strong>
        <p>{{ r.size }} • {{ r.date }}</p>
      </div>

      <div class="actions">
        <span class="check">✔</span>
        <button @click="removeResume(index)">✖</button>
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

/* Smooth fade-in */
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