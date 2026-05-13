<template>
  <div class="employee-dashboard">
    <!-- Top Header Bar -->
    <header class="topbar">
      <div class="topbar-left">
        <img src="/img/logo-bsp.jpg" alt="BSP Logo" class="topbar-logo">
        <h1 class="topbar-title">BSP Admin Dashboard</h1>
      </div>
      <div class="topbar-right">
        <span class="user-info">
          <span class="user-name">Eric (Owner)</span>
          <span class="role-badge role-owner">Owner</span>
        </span>
        <button class="btn btn-logout">
          🚪 登出
        </button>
      </div>
    </header>

    <div class="dashboard-body">
      <!-- Left Sidebar Navigation -->
      <aside class="sidebar">
        <nav class="sidebar-nav">
          <div class="nav-section">
            <NuxtLink to="/employee/dashboard" class="nav-item active">
              <span class="nav-icon">📊</span>
              <span>概覽 Overview</span>
            </NuxtLink>
            <NuxtLink to="/employee/schools" class="nav-item">
              <span class="nav-icon">🏫</span>
              <span>學校列表 Schools</span>
            </NuxtLink>
            <NuxtLink to="/employee/consultants" class="nav-item">
              <span class="nav-icon">👔</span>
              <span>顧問列表 Consultants</span>
            </NuxtLink>
            <NuxtLink to="/employee/payments" class="nav-item">
              <span class="nav-icon">💳</span>
              <span>付費管理 Payments</span>
            </NuxtLink>
            <NuxtLink to="/employee/discounts" class="nav-item">
              <span class="nav-icon">🎟️</span>
              <span>優惠碼 Discounts</span>
            </NuxtLink>
            <NuxtLink to="/employee/settings" class="nav-item">
              <span class="nav-icon">⚙️</span>
              <span>設定 Settings</span>
            </NuxtLink>
          </div>

          <!-- Permission Legend -->
          <div class="permission-legend">
            <h4>🔐 權限分级</h4>
            <div class="perm-item">
              <span class="role-badge role-employee">Employee</span>
              <p>協助學校/顧問更新資料、處理付費、優惠</p>
            </div>
            <div class="perm-item">
              <span class="role-badge role-owner">Owner</span>
              <p>完全權限：刪除帳戶、查看所有數據、系統設定</p>
            </div>
          </div>
        </nav>
      </aside>

      <!-- Main Content Area -->
      <main class="main-content">
        <!-- Welcome Banner -->
        <div class="welcome-banner">
          <div class="welcome-text">
            <h2>👋 歡迎回來，Eric！</h2>
            <p>以下是 BSP 系統概覽</p>
          </div>
          <div class="welcome-date">
            {{ currentDate }}
          </div>
        </div>

        <!-- Quick Stats Cards -->
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-icon">🏫</div>
            <div class="stat-info">
              <div class="stat-number">{{ stats.totalSchools }}</div>
              <div class="stat-label">學校總數 Schools</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">👔</div>
            <div class="stat-info">
              <div class="stat-number">{{ stats.totalConsultants }}</div>
              <div class="stat-label">顧問總數 Consultants</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">📝</div>
            <div class="stat-info">
              <div class="stat-number">{{ stats.todayApplications }}</div>
              <div class="stat-label">今日新申請 Today</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">💰</div>
            <div class="stat-info">
              <div class="stat-number">£{{ stats.pendingPayments }}</div>
              <div class="stat-label">待處理付款 Pending</div>
            </div>
          </div>
        </div>

        <!-- Recent Activity Section -->
        <div class="activity-section">
          <h3 class="section-title">📋 最新活動 Recent Activity</h3>
          <div class="activity-list">
            <div v-for="activity in recentActivities" :key="activity.id" class="activity-item">
              <span class="activity-icon">{{ activity.icon }}</span>
              <div class="activity-details">
                <div class="activity-text">{{ activity.text }}</div>
                <div class="activity-meta">{{ activity.time }} · {{ activity.user }}</div>
              </div>
              <span :class="['activity-badge', `badge-${activity.type}`]">{{ activity.typeLabel }}</span>
            </div>
          </div>
        </div>

        <!-- Quick Actions -->
        <div class="quick-actions">
          <h3 class="section-title">⚡ 快捷操作 Quick Actions</h3>
          <div class="actions-grid">
            <button class="action-btn">
              <span class="action-icon">➕</span>
              <span>新增學校</span>
            </button>
            <button class="action-btn">
              <span class="action-icon">👔</span>
              <span>新增顧問</span>
            </button>
            <button class="action-btn">
              <span class="action-icon">🎟️</span>
              <span>建立優惠碼</span>
            </button>
            <button class="action-btn">
              <span class="action-icon">📊</span>
              <span>查看報表</span>
            </button>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
// Placeholder data - to be replaced with backend API calls
const stats = ref({
  totalSchools: 24,
  totalConsultants: 12,
  todayApplications: 5,
  pendingPayments: 8750
})

const recentActivities = ref([
  {
    id: 1,
    icon: '🏫',
    text: '學校「Abbey College」更新了入學要求',
    time: '10 分鐘前',
    user: 'School Admin',
    type: 'school',
    typeLabel: '學校'
  },
  {
    id: 2,
    icon: '💳',
    text: '顧問「John Smith」提交了付費申請',
    time: '25 分鐘前',
    user: 'Consultant',
    type: 'payment',
    typeLabel: '付費'
  },
  {
    id: 3,
    icon: '🎟️',
    text: '新優惠碼「SUMMER20」已建立',
    time: '1 小時前',
    user: 'Eric',
    type: 'discount',
    typeLabel: '優惠'
  },
  {
    id: 4,
    icon: '📝',
    text: '學生「王小明」提交了 Harrow School 申請',
    time: '2 小時前',
    user: 'Student',
    type: 'application',
    typeLabel: '申請'
  },
  {
    id: 5,
    icon: '👔',
    text: '顧問「Mary Chen」更新了個人資料',
    time: '3 小時前',
    user: 'Consultant',
    type: 'consultant',
    typeLabel: '顧問'
  }
])

const currentDate = computed(() => {
  const now = new Date()
  return now.toLocaleDateString('zh-HK', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    weekday: 'long'
  })
})
</script>

<style scoped>
/* BSP Employee Dashboard Styles */
:root {
  --bsp-primary: #212E54;
  --bsp-secondary: #3b82f6;
  --bsp-accent: #C1AA78;
  --bsp-dark: #1e293b;
  --bsp-light: #f8fafc;
  --bsp-success: #10b981;
  --bsp-warning: #f59e0b;
  --bsp-danger: #ef4444;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: var(--bsp-dark);
  background: var(--bsp-light);
}

.employee-dashboard {
  min-height: 100vh;
  background: var(--bsp-light);
}

/* Top Header Bar */
.topbar {
  background: var(--bsp-primary);
  color: white;
  padding: 0.75rem 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  position: sticky;
  top: 0;
  z-index: 100;
}

.topbar-left {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.topbar-logo {
  height: 40px;
  width: auto;
  border-radius: 4px;
}

.topbar-title {
  font-size: 1.1rem;
  font-weight: 600;
  letter-spacing: 0.02em;
}

.topbar-right {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.user-name {
  font-weight: 500;
}

.role-badge {
  padding: 0.2rem 0.6rem;
  border-radius: 9999px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
}

.role-owner {
  background: var(--bsp-accent);
  color: var(--bsp-primary);
}

.role-employee {
  background: var(--bsp-secondary);
  color: white;
}

.btn-logout {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  border: 1px solid rgba(255, 255, 255, 0.3);
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s;
}

.btn-logout:hover {
  background: rgba(255, 255, 255, 0.25);
}

/* Dashboard Body Layout */
.dashboard-body {
  display: flex;
  min-height: calc(100vh - 58px);
}

/* Sidebar */
.sidebar {
  width: 260px;
  background: white;
  border-right: 1px solid #e2e8f0;
  padding: 1.5rem 0;
  flex-shrink: 0;
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.nav-section {
  flex: 1;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.875rem 1.5rem;
  color: #64748b;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s;
  border-left: 3px solid transparent;
}

.nav-item:hover {
  background: #f1f5f9;
  color: var(--bsp-primary);
}

.nav-item.active {
  background: #eff6ff;
  color: var(--bsp-primary);
  border-left-color: var(--bsp-secondary);
  font-weight: 600;
}

.nav-icon {
  font-size: 1.1rem;
}

/* Permission Legend */
.permission-legend {
  margin: 1rem;
  padding: 1rem;
  background: #f8fafc;
  border-radius: 8px;
  border: 1px solid #e2e8f0;
}

.permission-legend h4 {
  font-size: 0.85rem;
  color: var(--bsp-dark);
  margin-bottom: 0.75rem;
  font-weight: 600;
}

.perm-item {
  margin-bottom: 0.75rem;
}

.perm-item:last-child {
  margin-bottom: 0;
}

.perm-item p {
  font-size: 0.75rem;
  color: #64748b;
  margin-top: 0.25rem;
  line-height: 1.4;
}

/* Main Content */
.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
  overflow-y: auto;
}

/* Welcome Banner */
.welcome-banner {
  background: linear-gradient(135deg, var(--bsp-primary) 0%, #2d3f6b 100%);
  color: white;
  padding: 1.5rem 2rem;
  border-radius: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
  box-shadow: 0 4px 12px rgba(33, 46, 84, 0.25);
}

.welcome-text h2 {
  font-size: 1.5rem;
  font-weight: 700;
  margin-bottom: 0.25rem;
}

.welcome-text p {
  opacity: 0.85;
  font-size: 0.95rem;
}

.welcome-date {
  font-size: 0.9rem;
  opacity: 0.85;
}

/* Stats Grid */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
  margin-bottom: 2rem;
}

.stat-card {
  background: white;
  border-radius: 10px;
  padding: 1.25rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  transition: transform 0.2s, box-shadow 0.2s;
}

.stat-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-icon {
  font-size: 2rem;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f1f5f9;
  border-radius: 10px;
}

.stat-number {
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--bsp-primary);
  line-height: 1;
}

.stat-label {
  font-size: 0.8rem;
  color: #64748b;
  margin-top: 0.25rem;
}

/* Activity Section */
.activity-section {
  background: white;
  border-radius: 10px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  margin-bottom: 1.5rem;
}

.section-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--bsp-dark);
  margin-bottom: 1rem;
  padding-bottom: 0.75rem;
  border-bottom: 1px solid #e2e8f0;
}

.activity-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.activity-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem;
  background: #f8fafc;
  border-radius: 8px;
  transition: background 0.2s;
}

.activity-item:hover {
  background: #f1f5f9;
}

.activity-icon {
  font-size: 1.5rem;
  width: 40px;
  text-align: center;
}

.activity-details {
  flex: 1;
}

.activity-text {
  font-weight: 500;
  color: var(--bsp-dark);
  font-size: 0.9rem;
}

.activity-meta {
  font-size: 0.75rem;
  color: #94a3b8;
  margin-top: 0.2rem;
}

.activity-badge {
  padding: 0.25rem 0.6rem;
  border-radius: 4px;
  font-size: 0.7rem;
  font-weight: 600;
}

.badge-school { background: #dbeafe; color: #1d4ed8; }
.badge-payment { background: #d1fae5; color: #047857; }
.badge-discount { background: #fef3c7; color: #b45309; }
.badge-application { background: #ede9fe; color: #6d28d9; }
.badge-consultant { background: #fce7f3; color: #be185d; }

/* Quick Actions */
.quick-actions {
  background: white;
  border-radius: 10px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
}

.actions-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
}

.action-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  padding: 1.25rem;
  background: #f8fafc;
  border: 2px dashed #cbd5e1;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.2s;
  color: var(--bsp-dark);
  font-weight: 500;
  font-size: 0.85rem;
}

.action-btn:hover {
  background: #eff6ff;
  border-color: var(--bsp-secondary);
  color: var(--bsp-primary);
}

.action-icon {
  font-size: 1.75rem;
}

/* Responsive */
@media (max-width: 1024px) {
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  .actions-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .sidebar {
    display: none;
  }
  .dashboard-body {
    flex-direction: column;
  }
  .stats-grid {
    grid-template-columns: 1fr;
  }
  .actions-grid {
    grid-template-columns: 1fr 1fr;
  }
}
</style>
