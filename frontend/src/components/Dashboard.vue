<script setup>
import { ref, computed } from 'vue'
import JobForm from './JobForm.vue'
import ResumeForm from './ResumeForm.vue'


// JOB VALUES
const showJobForm = ref(false)
const props = defineProps(['savedJobs'])

function saveJob() {
  console.log("SAVE CLICKED") // 👈 ADD THIS

  if (!jobTitle.value || !jobDescription.value) return

  emit('save', {
    title: jobTitle.value,
    description: jobDescription.value,
    skills: requiredSkills.value
  })

  emit('close')
}

function addJob(job) {
  props.savedJobs.push(job)
}

// RESUME VALUES
const showResumeForm = ref(false)
const savedResumes = ref([])

function saveResume() {
  console.log("SAVE CLICKED") // 👈 ADD THIS

  if (!ResumeTitle.value || !ResumeDescription.value) return

  emit('save', {
    title: ResumeTitle.value,
    description: ResumeDescription.value,
    skills: requiredSkills.value
  })

  emit('close')
}

function addResume(Resume) {
  savedResumes.value.push(Resume)

  activity.value.unshift({
    title: "Resume posting added",
    name: Resume.title,
    time: "Just now"
  })
}

const stats = computed(() => [
  { title: "Total Resumes", value: savedResumes.value.length, icon: "📄" },
  { title: "Job Postings", value: props.savedJobs.length, icon: "💼" },
  { title: "Screened", value: 89, icon: "✔️" },
  { title: "Pending", value: 38, icon: "⏱️" }
])

const activity = ref([
  { title: "Resume uploaded", name: "Sarah Johnson", time: "2 minutes ago" },
  { title: "Resume screened", name: "Michael Chen", time: "15 minutes ago" },
  { title: "Job posting added", name: "Senior Developer", time: "1 hour ago" },
  { title: "Resume uploaded", name: "Emily Rodriguez", time: "2 hours ago" }
])
</script>

<template>
<div class="content">

  <div class="header">
    <h1>Dashboard Overview</h1>
    <p>Monitor your recruitment pipeline at a glance</p>
  </div>

  <div class="stats">
    <div class="stat-card" v-for="s in stats" :key="s.title">
      <div>
        <p class="stat-title">{{ s.title }}</p>
        <h2>{{ s.value }}</h2>
      </div>
      <span class="icon">{{ s.icon }}</span>
    </div>
  </div>

  <div class="actions">

    <div class="action-card" @click="showResumeForm = true">
      <h3>Upload Resume</h3>
      <p>Upload and parse candidate resumes for AI screening</p>
    </div>

    <div class="action-card" @click="showJobForm = true">
      <h3>New Job Posting</h3>
      <p>Add new job postings to match against candidates</p>
    </div>

    <div class="action-card">
      <h3>Screen Resumes</h3>
      <p>Review and screen candidates with AI assistance</p>
    </div>

  </div>

  <JobForm
    v-if="showJobForm"
    @close="showJobForm = false"
    @save="addJob"
  />
  <ResumeForm
    v-if="showResumeForm"
    @close="showResumeForm = false"
    @save="addResume"
  />

  <div class="activity">

    <h3>Recent Activity</h3>

    <div class="activity-item" v-for="a in activity" :key="a.name">

      <div>
        <p class="activity-title">{{ a.title }}</p>
        <span>{{ a.name }}</span>
      </div>

      <small>{{ a.time }}</small>

    </div>

  </div>

</div>
</template>