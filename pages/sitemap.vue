<template>
  <div class="sitemap-page">
    <div class="sitemap-header">
      <h1>🗺️ Sitemap</h1>
      <p>British School Portal — All Available Pages</p>
      <p class="updated">Version: 03fdf299 | Updated: {{ currentDate }}</p>
    </div>

    <div class="sitemap-grid">
      <!-- Public Pages -->
      <div class="sitemap-section">
        <h2>🌐 Public Pages</h2>
        <ul>
          <li><NuxtLink to="/">Home</NuxtLink></li>
          <li><NuxtLink to="/search">Search</NuxtLink></li>
          <li><NuxtLink to="/consultants">Consultants Directory</NuxtLink></li>
          <li><NuxtLink to="/login">Login</NuxtLink></li>
          <li><NuxtLink to="/register">Register</NuxtLink></li>
        </ul>
      </div>

      <!-- Student Portal -->
      <div class="sitemap-section">
        <h2>🎓 Student Portal</h2>
        <ul>
          <li><NuxtLink to="/student/login">Student Login</NuxtLink></li>
          <li><NuxtLink to="/student/register">Student Registration</NuxtLink></li>
          <li><NuxtLink to="/student/applications">My Applications</NuxtLink></li>
          <li><NuxtLink to="/student/profile">Student Profile</NuxtLink></li>
        </ul>
      </div>

      <!-- Consultant Yellow Pages -->
      <div class="sitemap-section">
        <h2>👔 Consultant Profiles</h2>
        <ul>
          <li><NuxtLink to="/consultant/hong-kong/victoria-education-group">Victoria Education Group (HK)</NuxtLink></li>
          <li><NuxtLink to="/consultant/hong-kong/global-education-consultants">Global Education Consultants (HK)</NuxtLink></li>
          <li><NuxtLink to="/consultant/singapore/elite-academic-advisors">Elite Academic Advisors (SG)</NuxtLink></li>
          <li><NuxtLink to="/consultant/dubai/pinnacle-study-solutions">Pinnacle Study Solutions (UAE)</NuxtLink></li>
        </ul>
      </div>

      <!-- BSP Admin -->
      <div class="sitemap-section">
        <h2>⚙️ BSP Administration</h2>
        <ul>
          <li><NuxtLink to="/BSP/dashboard">BSP Dashboard</NuxtLink></li>
          <li><NuxtLink to="/BSP/users/personal">Personal Users</NuxtLink></li>
          <li><NuxtLink to="/BSP/users/school">School Users</NuxtLink></li>
          <li><NuxtLink to="/BSP/users/consultant">Consultant Users</NuxtLink></li>
          <li><NuxtLink to="/BSP/users/bspstaff">BSP Staff</NuxtLink></li>
          <li><NuxtLink to="/BSP/new-account-applications">New Account Applications</NuxtLink></li>
          <li><NuxtLink to="/BSP/payments">Payments</NuxtLink></li>
        </ul>
      </div>

      <!-- Legal -->
      <div class="sitemap-section">
        <h2>📜 Legal</h2>
        <ul>
          <li><NuxtLink to="/terms">Terms of Service</NuxtLink></li>
          <li><NuxtLink to="/privacy">Privacy Policy</NuxtLink></li>
          <li><NuxtLink to="/faq">FAQ</NuxtLink></li>
        </ul>
      </div>
    </div>

    <div class="sitemap-footer">
      <p>© 2026 British School Portal. All rights reserved.</p>
      <p>Deployed URL: <a :href="deployedUrl" target="_blank">{{ deployedUrl }}</a></p>
    </div>
  </div>
</template>

<script setup>
const currentDate = new Date().toLocaleDateString('en-GB', {
  year: 'numeric',
  month: 'long',
  day: 'numeric'
})

const deployedUrl = 'https://03fdf299.bsp-v3.pages.dev'
</script>

<style scoped>
.sitemap-page {
  max-width: 1000px;
  margin: 0 auto;
  padding: 40px 20px;
}

.sitemap-header {
  text-align: center;
  margin-bottom: 50px;
  padding-bottom: 30px;
  border-bottom: 2px solid #e5e7eb;
}

.sitemap-header h1 {
  font-size: 2.5rem;
  color: #1a1a2e;
  margin-bottom: 10px;
}

.sitemap-header p {
  color: #6b7280;
  font-size: 1.1rem;
}

.sitemap-header .updated {
  font-size: 0.9rem;
  color: #9ca3af;
  margin-top: 10px;
}

.sitemap-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 30px;
  margin-bottom: 50px;
}

.sitemap-section {
  background: #f9fafb;
  border-radius: 12px;
  padding: 25px;
  border: 1px solid #e5e7eb;
}

.sitemap-section h2 {
  font-size: 1.2rem;
  color: #1a1a2e;
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 2px solid #3b82f6;
}

.sitemap-section ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.sitemap-section li {
  padding: 8px 0;
  border-bottom: 1px solid #f3f4f6;
}

.sitemap-section li:last-child {
  border-bottom: none;
}

.sitemap-section a {
  color: #3b82f6;
  text-decoration: none;
  font-size: 0.95rem;
  transition: color 0.2s;
}

.sitemap-section a:hover {
  color: #1d4ed8;
  text-decoration: underline;
}

.sitemap-footer {
  text-align: center;
  padding-top: 30px;
  border-top: 2px solid #e5e7eb;
  color: #6b7280;
  font-size: 0.9rem;
}

.sitemap-footer a {
  color: #3b82f6;
}
</style>
