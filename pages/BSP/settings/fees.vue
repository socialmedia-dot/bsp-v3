<template>
  <div class="bsp-dashboard">
    <div class="dashboard-body">
      <!-- Left Sidebar Navigation -->
      <aside class="sidebar">
        <nav class="sidebar-nav">
          <div class="nav-section">
            <div class="nav-section-title">User Categories</div>
            <NuxtLink to="/BSP/users/personal" class="nav-item">
              <span class="nav-icon">👤</span>
              <span>Personal</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/school" class="nav-item">
              <span class="nav-icon">🏫</span>
              <span>School</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/consultant" class="nav-item">
              <span class="nav-icon">💼</span>
              <span>Consultant</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/bspstaff" class="nav-item">
              <span class="nav-icon">👔</span>
              <span>BSP Staff</span>
            </NuxtLink>
          </div>

          <div class="nav-section">
            <div class="nav-section-title">Management</div>
            <NuxtLink to="/BSP/dashboard" class="nav-item">
              <span class="nav-icon">📊</span>
              <span>Overview</span>
            </NuxtLink>
            <NuxtLink to="/BSP/new-account-applications" class="nav-item">
              <span class="nav-icon">📋</span>
              <span>New Account Applications</span>
            </NuxtLink>
            <NuxtLink to="/BSP/payments" class="nav-item">
              <span class="nav-icon">💳</span>
              <span>Payments</span>
            </NuxtLink>
          </div>

          <div class="nav-section">
            <div class="nav-section-title">Settings</div>
            <NuxtLink to="/BSP/settings/fees" class="nav-item active">
              <span class="nav-icon">💰</span>
              <span>Annual Fee</span>
            </NuxtLink>
            <NuxtLink to="/BSP/settings/website" class="nav-item">
              <span class="nav-icon">🌐</span>
              <span>Website Settings</span>
            </NuxtLink>
            <NuxtLink to="/BSP/settings/staff" class="nav-item">
              <span class="nav-icon">👔</span>
              <span>Staff</span>
            </NuxtLink>
          </div>
        </nav>
      </aside>

      <!-- Main Content Area -->
      <main class="main-content">
        <div class="page-header">
          <div class="page-title-area">
            <h1 class="page-title">💰 Annual Fee Settings</h1>
            <p class="page-subtitle">Manage membership fees for all user types</p>
          </div>
        </div>

        <!-- Fee Tiers -->
        <div class="settings-section">
          <div class="section-header">
            <h2 class="section-title">Fee Tiers</h2>
            <button class="btn btn-primary" @click="showAddTier = true">+ Add Tier</button>
          </div>

          <div class="fees-table-wrapper">
            <table class="fees-table">
              <thead>
                <tr>
                  <th>User Type</th>
                  <th>Annual Fee</th>
                  <th>Currency</th>
                  <th>Status</th>
                  <th>Last Updated</th>
                  <th>Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="tier in feeTiers" :key="tier.id">
                  <td>
                    <div class="type-cell">
                      <span class="type-badge" :class="'type-' + tier.userType">{{ tier.userType }}</span>
                      <span class="tier-name">{{ tier.name }}</span>
                    </div>
                  </td>
                  <td><span class="fee-amount">£{{ tier.amount }}</span></td>
                  <td>{{ tier.currency }}</td>
                  <td>
                    <label class="toggle-switch">
                      <input type="checkbox" v-model="tier.active" />
                      <span class="toggle-slider"></span>
                    </label>
                  </td>
                  <td class="text-muted">{{ tier.updatedAt }}</td>
                  <td>
                    <div class="action-buttons">
                      <button class="btn-action" @click="editTier(tier)" title="Edit">✏️</button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Billing Cycle -->
        <div class="settings-section">
          <div class="section-header">
            <h2 class="section-title">Billing Cycle</h2>
          </div>
          <div class="form-grid">
            <div class="form-group">
              <label class="form-label">Renewal Date (Annual)</label>
              <input type="text" v-model="billingSettings.renewalDate" class="form-input" readonly />
              <span class="form-hint">All memberships renew on this date each year</span>
            </div>
            <div class="form-group">
              <label class="form-label">Grace Period (days)</label>
              <input type="number" v-model="billingSettings.gracePeriod" class="form-input" />
            </div>
            <div class="form-group">
              <label class="form-label">Reminder Before Due (days)</label>
              <input type="number" v-model="billingSettings.reminderDays" class="form-input" />
            </div>
            <div class="form-group">
              <label class="form-label">Late Fee (%)</label>
              <input type="number" step="0.1" v-model="billingSettings.lateFeePercent" class="form-input" />
            </div>
          </div>
        </div>

        <!-- Mid-Term Join Settings -->
        <div class="settings-section">
          <div class="section-header">
            <h2 class="section-title">Mid-Term Join Settings</h2>
            <span class="section-hint">Users joining between renewal cycles</span>
          </div>
          <div class="form-grid">
            <div class="form-group full-width">
              <label class="form-label">Billing Rule for Mid-Term Joiners</label>
              <select v-model="billingSettings.midTermPolicy" class="form-input">
                <option value="pro_rata">Pro-rata (charge remaining months until next renewal)</option>
                <option value="full_year">Full Year (charge full annual fee, valid until next renewal)</option>
                <option value="fixed_period">Fixed Period (minimum months charged)</option>
              </select>
            </div>
            <div v-if="billingSettings.midTermPolicy === 'fixed_period'" class="form-group">
              <label class="form-label">Minimum Charge (months)</label>
              <input type="number" min="1" max="12" v-model="billingSettings.minMonths" class="form-input" />
            </div>
            <div class="form-group">
              <label class="form-label">Cut-off Month (no pro-rata after this)</label>
              <select v-model="billingSettings.cutOffMonth" class="form-input">
                <option v-for="m in 12" :key="m" :value="m">{{ monthNames[m-1] }}</option>
              </select>
              <span class="form-hint">After this month, charge full year to next renewal</span>
            </div>
          </div>

          <!-- Example Calculation -->
          <div class="calc-preview">
            <h3 class="calc-title">Example Calculation</h3>
            <div class="calc-row">
              <span class="calc-label">School joins in March (£299/year):</span>
              <span class="calc-value">{{ midTermExample.march }}</span>
            </div>
            <div class="calc-row">
              <span class="calc-label">Consultant joins in August (£199/year):</span>
              <span class="calc-value">{{ midTermExample.august }}</span>
            </div>
            <div class="calc-row">
              <span class="calc-label">Personal joins in September (£99/year):</span>
              <span class="calc-value">{{ midTermExample.september }}</span>
            </div>
          </div>
        </div>

        <!-- Save Button -->
        <div class="settings-footer">
          <button class="btn btn-primary" @click="saveSettings">💾 Save Changes</button>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
const feeTiers = ref([
  { id: 1, userType: 'school', name: 'School Membership', amount: 299, currency: 'GBP', active: true, updatedAt: '2026-04-15' },
  { id: 2, userType: 'consultant', name: 'Consultant Membership', amount: 199, currency: 'GBP', active: true, updatedAt: '2026-04-15' },
  { id: 3, userType: 'personal', name: 'Personal Membership', amount: 99, currency: 'GBP', active: true, updatedAt: '2026-04-15' },
])

const monthNames = ['January', 'February', 'March', 'April', 'May', 'June',
  'July', 'August', 'September', 'October', 'November', 'December']

const billingSettings = ref({
  renewalDate: '01 October',
  gracePeriod: 30,
  reminderDays: 7,
  lateFeePercent: 5.0,
  midTermPolicy: 'pro_rata',
  minMonths: 3,
  cutOffMonth: 7, // July
})

// Mid-term join example calculations
const midTermExample = computed(() => {
  const policy = billingSettings.value.midTermPolicy
  const cutOff = billingSettings.value.cutOffMonth
  const minMonths = billingSettings.value.minMonths || 1

  const calc = (amount: number, joinMonth: number) => {
    const monthsToOct = joinMonth <= 10 ? (10 - joinMonth) : (10 + 12 - joinMonth)
    const nextRenewalYear = joinMonth <= 10 ? new Date().getFullYear() : new Date().getFullYear() + 1

    if (policy === 'full_year') {
      return `£${amount} full year → expires 01 Oct ${nextRenewalYear}`
    }
    if (policy === 'fixed_period') {
      const chargeMonths = Math.max(monthsToOct, minMonths)
      const charge = Math.round((amount / 12) * chargeMonths)
      return `£${charge} (${chargeMonths} months min) → expires 01 Oct ${nextRenewalYear}`
    }
    // pro_rata
    if (joinMonth > cutOff) {
      return `£${amount} full year (after cut-off) → expires 01 Oct ${nextRenewalYear + 1}`
    }
    const charge = Math.round((amount / 12) * monthsToOct)
    return `£${charge} (${monthsToOct} months pro-rata) → expires 01 Oct ${nextRenewalYear}`
  }

  return {
    march: calc(299, 3),      // School
    august: calc(199, 8),     // Consultant
    september: calc(99, 9),   // Personal
  }
})

const showAddTier = ref(false)

const editTier = (tier: typeof feeTiers.value[0]) => {
  // Edit tier logic
}

const saveSettings = () => {
  alert('Settings saved!')
}
</script>

<style scoped>
.bsp-dashboard { min-height: 100vh; background: var(--bsp-light, #f8fafc); font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; color: var(--bsp-dark, #1e293b); }
.dashboard-body { display: flex; min-height: 100vh; }

/* Sidebar */
.sidebar { width: 260px; background: white; border-right: 1px solid #e2e8f0; padding: 1.5rem 0; flex-shrink: 0; position: sticky; top: 0; height: 100vh; overflow-y: auto; }
.nav-section { margin-bottom: 1.5rem; }
.nav-section-title { font-size: 0.7rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.08em; color: #94a3b8; padding: 0 1.5rem; margin-bottom: 0.5rem; }
.nav-item { display: flex; align-items: center; gap: 0.75rem; padding: 0.875rem 1.5rem; color: #64748b; text-decoration: none; font-weight: 500; font-size: 0.9rem; transition: all 0.2s; border-left: 3px solid transparent; }
.nav-item:hover { background: #f1f5f9; color: var(--bsp-primary, #212E54); }
.nav-item.active { background: #eff6ff; color: var(--bsp-primary, #212E54); border-left-color: var(--bsp-secondary, #3b82f6); font-weight: 600; }
.nav-icon { font-size: 1.1rem; }

/* Main Content */
.main-content { flex: 1; padding: 2rem; max-width: 1200px; }
.page-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; }
.page-title { font-size: 1.5rem; font-weight: 700; color: var(--bsp-primary, #212E54); margin-bottom: 0.25rem; }
.page-subtitle { font-size: 0.9rem; color: #64748b; }

/* Settings Section */
.settings-section { background: white; border-radius: 10px; padding: 1.5rem; margin-bottom: 1.5rem; box-shadow: 0 1px 3px rgba(0,0,0,0.08); }
.section-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.25rem; }
.section-title { font-size: 1.1rem; font-weight: 600; color: var(--bsp-dark, #1e293b); }

/* Fees Table */
.fees-table-wrapper { overflow-x: auto; }
.fees-table { width: 100%; border-collapse: collapse; font-size: 0.9rem; }
.fees-table th { text-align: left; padding: 0.875rem 1rem; font-size: 0.75rem; font-weight: 700; color: #64748b; text-transform: uppercase; letter-spacing: 0.05em; border-bottom: 1px solid #e2e8f0; }
.fees-table td { padding: 1rem; border-bottom: 1px solid #f1f5f9; vertical-align: middle; }
.type-cell { display: flex; align-items: center; gap: 0.75rem; }
.type-badge { padding: 0.25rem 0.75rem; border-radius: 20px; font-size: 0.75rem; font-weight: 600; text-transform: capitalize; }
.type-school { background: #dbeafe; color: #1e40af; }
.type-consultant { background: #fef3c7; color: #92400e; }
.type-personal { background: #d1fae5; color: #047857; }
.tier-name { font-weight: 600; color: var(--bsp-dark, #1e293b); }
.fee-amount { font-size: 1.1rem; font-weight: 700; color: var(--bsp-primary, #212E54); }
.text-muted { color: #94a3b8; font-size: 0.85rem; }

/* Toggle Switch */
.toggle-switch { position: relative; display: inline-block; width: 44px; height: 24px; }
.toggle-switch input { opacity: 0; width: 0; height: 0; }
.toggle-slider { position: absolute; cursor: pointer; inset: 0; background: #cbd5e1; border-radius: 24px; transition: 0.3s; }
.toggle-slider::before { content: ''; position: absolute; height: 18px; width: 18px; left: 3px; bottom: 3px; background: white; border-radius: 50%; transition: 0.3s; }
.toggle-switch input:checked + .toggle-slider { background: var(--bsp-success, #10b981); }
.toggle-switch input:checked + .toggle-slider::before { transform: translateX(20px); }

/* Form Grid */
.form-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 1.25rem; }
.form-group { display: flex; flex-direction: column; gap: 0.4rem; }
.form-group.full-width { grid-column: 1 / -1; }
.form-label { font-size: 0.8rem; font-weight: 600; color: #64748b; }
.form-input { padding: 0.65rem 0.875rem; border: 1px solid #e2e8f0; border-radius: 6px; font-size: 0.9rem; font-family: inherit; }
.form-input:focus { outline: none; border-color: var(--bsp-secondary, #3b82f6); box-shadow: 0 0 0 3px rgba(59,130,246,0.1); }
.form-hint { font-size: 0.75rem; color: #94a3b8; margin-top: 0.15rem; }
.section-hint { font-size: 0.8rem; color: #94a3b8; }

/* Calculation Preview */
.calc-preview { background: #f8fafc; border: 1px solid #e2e8f0; border-radius: 8px; padding: 1rem 1.25rem; margin-top: 1rem; }
.calc-title { font-size: 0.85rem; font-weight: 600; color: #475569; margin-bottom: 0.75rem; }
.calc-row { display: flex; justify-content: space-between; padding: 0.4rem 0; font-size: 0.85rem; border-bottom: 1px solid #f1f5f9; }
.calc-row:last-child { border-bottom: none; }
.calc-label { color: #64748b; }
.calc-value { font-weight: 600; color: var(--bsp-primary, #212E54); }

/* Buttons */
.btn { padding: 0.6rem 1.25rem; border-radius: 6px; font-size: 0.85rem; font-weight: 600; cursor: pointer; border: 1px solid transparent; transition: all 0.2s; }
.btn-primary { background: var(--bsp-secondary, #3b82f6); color: white; }
.btn-primary:hover { background: #2563eb; }
.btn-action { background: none; border: none; font-size: 1rem; cursor: pointer; padding: 0.35rem; border-radius: 4px; }
.btn-action:hover { background: #f1f5f9; }

/* Footer */
.settings-footer { display: flex; justify-content: flex-end; }

/* Responsive */
@media (max-width: 768px) {
  .sidebar { display: none; }
  .main-content { padding: 1rem; }
  .form-grid { grid-template-columns: 1fr; }
}
</style>
