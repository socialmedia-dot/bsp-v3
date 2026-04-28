<template>
  <div class="container" style="margin-top: 2rem;">
    <div style="text-align: center; margin-bottom: 2rem;">
      <h1 style="font-size: 2rem; margin-bottom: 0.5rem;">🏫 Find UK Schools</h1>
      <p style="color: #64748b; margin-bottom: 0;">Compare independent schools across the United Kingdom</p>
    </div>
    
    <!-- Search Box -->
    <div class="search-box" style="margin-bottom: 2rem;">
      <form class="search-form" @submit.prevent="filterSchools">
        <div class="form-group">
          <label>Search</label>
          <input v-model="filters.search" type="text" placeholder="School name..." />
        </div>
        <div class="form-group">
          <label>Location</label>
          <select v-model="filters.location">
            <option value="">All Regions</option>
            <option value="london">London</option>
            <option value="south-east">South East</option>
            <option value="south-west">South West</option>
            <option value="midlands">Midlands</option>
            <option value="north">Northern England</option>
            <option value="scotland">Scotland</option>
            <option value="wales">Wales</option>
          </select>
        </div>
        <div class="form-group">
          <label>Gender</label>
          <select v-model="filters.gender">
            <option value="">All</option>
            <option value="boys">Boys Only</option>
            <option value="girls">Girls Only</option>
            <option value="co-ed">Co-educational</option>
          </select>
        </div>
        <div class="form-group">
          <label>Boarding</label>
          <select v-model="filters.boardType">
            <option value="">All Types</option>
            <option value="full">Full Boarding</option>
            <option value="weekly">Weekly Boarding</option>
            <option value="day">Day School</option>
            <option value="flexi">Flexi Boarding</option>
          </select>
        </div>
        <div class="form-group" style="justify-content: flex-end;">
          <button type="submit" class="btn btn-primary" style="width: 100%;">Search</button>
        </div>
      </form>
    </div>
    
    <!-- Results Count -->
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem;">
      <p>{{ filteredSchools.length }} schools found</p>
      <NuxtLink to="/login" class="btn btn-secondary" style="padding: 0.5rem 1rem; font-size: 0.875rem;">
        Apply Now →
      </NuxtLink>
    </div>
    
    <!-- School Cards -->
    <div class="card-grid">
      <div v-for="school in filteredSchools" :key="school.id" class="card">
        <div class="card-image">🏫</div>
        <div class="card-content">
          <h3 class="card-title">{{ school.name }}</h3>
          <p class="card-meta">📍 {{ school.location }}</p>
          <div style="display: flex; gap: 0.5rem; margin-bottom: 1rem; flex-wrap: wrap;">
            <span style="background: #eff6ff; color: var(--bsp-primary); padding: 0.25rem 0.75rem; border-radius: 9999px; font-size: 0.75rem; font-weight: 600;">
              {{ school.gender }}
            </span>
            <span style="background: #f0fdf4; color: var(--bsp-success); padding: 0.25rem 0.75rem; border-radius: 9999px; font-size: 0.75rem; font-weight: 600;">
              {{ school.boardType }}
            </span>
          </div>
          <p style="color: #64748b; font-size: 0.875rem; margin-bottom: 1rem;">
            {{ school.ageRange }} • {{ school.exams }}
          </p>
          <div style="display: flex; justify-content: space-between; align-items: center;">
            <span style="font-weight: 700; color: var(--bsp-primary);">{{ school.fee }}</span>
            <NuxtLink :to="`/schools`" class="btn btn-secondary" style="padding: 0.5rem 1rem; font-size: 0.875rem;">
              View →
            </NuxtLink>
          </div>
        </div>
      </div>
    </div>
    
    <!-- CTA -->
    <div style="background: linear-gradient(135deg, var(--bsp-primary) 0%, var(--bsp-secondary) 100%); color: white; padding: 3rem; border-radius: 1rem; margin-top: 3rem; text-align: center;">
      <h2 style="margin-bottom: 1rem;">Ready to Apply?</h2>
      <p style="opacity: 0.9; margin-bottom: 1.5rem;">Create an account to start your application process</p>
      <NuxtLink to="/register" class="btn" style="background: white; color: var(--bsp-primary); padding: 0.75rem 2rem;">
        Register Free →
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>

const { setMeta } = useSEO()
setMeta({
  title: 'British School Portal | Find Best UK Schools',
  description: 'Browse 100+ UK independent schools, compare tuition fees, admission requirements, and apply online. The trusted platform for international student applications.',
  path: '/',
  type: 'website'
})

const filters = ref({
  search: '',
  location: '',
  gender: '',
  boardType: ''
})

const schools = ref([
  {
    id: 'demo-school-01',
    name: 'Demo School 01',
    location: 'London, SW1A 1AA',
    gender: 'Co-educational',
    boardType: 'Full Boarding',
    ageRange: 'Ages 3-18',
    exams: 'GCSE, A-Level, IB',
    fee: 'From £16,500/term'
  },
  {
    id: 'demo-school-02',
    name: 'Demo School 02',
    location: 'Oxford, OX1 2AB',
    gender: 'Co-educational',
    boardType: 'Full Boarding',
    ageRange: 'Ages 11-18',
    exams: 'GCSE, IGCSE, A-Level',
    fee: 'From £14,400/term'
  },
  {
    id: 'bsp-demo',
    name: 'BS Portal Demo School',
    location: 'Leeds, LS1 3AB',
    gender: 'Boys Only',
    boardType: 'Full Boarding',
    ageRange: 'Ages 7-18',
    exams: 'GCSE, A-Level, Pre-U, IB',
    fee: 'From £18,600/term'
  }
])

const filteredSchools = computed(() => {
  return schools.value.filter(school => {
    const matchesSearch = !filters.value.search || 
      school.name.toLowerCase().includes(filters.value.search.toLowerCase())
    const matchesLocation = !filters.value.location || 
      school.location.toLowerCase().includes(filters.value.location)
    const matchesGender = !filters.value.gender || 
      school.gender.toLowerCase().includes(filters.value.gender)
    const matchesBoard = !filters.value.boardType || 
      school.boardType.toLowerCase().includes(filters.value.boardType)
    return matchesSearch && matchesLocation && matchesGender && matchesBoard
  })
})

const filterSchools = () => {
  console.log('Filtering with:', filters.value)
}
</script>
