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
              <span class="nav-count">12</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/school" class="nav-item">
              <span class="nav-icon">🏫</span>
              <span>School</span>
              <span class="nav-count">24</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/consultant" class="nav-item">
              <span class="nav-icon">💼</span>
              <span>Consultant</span>
              <span class="nav-count">8</span>
            </NuxtLink>
            <NuxtLink to="/BSP/users/bspstaff" class="nav-item">
              <span class="nav-icon">👔</span>
              <span>BSP Staff</span>
              <span class="nav-count">4</span>
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
            <NuxtLink to="/BSP/payments" class="nav-item active">
              <span class="nav-icon">💳</span>
              <span>Payments</span>
            </NuxtLink>
          </div>

          <div class="nav-section">
            <div class="nav-section-title">Settings</div>
            <NuxtLink to="/BSP/settings/fees" class="nav-item">
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
        <!-- Page Header -->
        <div class="page-header">
          <div class="page-title-area">
            <h1 class="page-title">💳 Payments</h1>
            <p class="page-subtitle">Manage payments, invoices and refunds</p>
          </div>
          <div class="page-actions">
            <button class="btn btn-secondary" @click="exportPayments">
              📥 Export CSV
            </button>
          </div>
        </div>

        <!-- Stats Cards -->
        <div class="stats-grid">
          <div class="stat-card stat-card-highlight">
            <div class="stat-icon">💰</div>
            <div class="stat-info">
              <div class="stat-number">£{{ totalRevenue.toLocaleString() }}</div>
              <div class="stat-label">Total Revenue</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">⏳</div>
            <div class="stat-info">
              <div class="stat-number">{{ pendingCount }}</div>
              <div class="stat-label">Pending</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">✅</div>
            <div class="stat-info">
              <div class="stat-number">{{ completedCount }}</div>
              <div class="stat-label">Completed</div>
            </div>
          </div>
          <div class="stat-card">
            <div class="stat-icon">↩️</div>
            <div class="stat-info">
              <div class="stat-number">{{ refundedCount }}</div>
              <div class="stat-label">Refunded</div>
            </div>
          </div>
        </div>

        <!-- Tabs -->
        <div class="tab-bar">
          <button class="tab-btn" :class="{ active: activeTab === 'all' }" @click="activeTab = 'all'">
            All Payments <span class="tab-count">{{ payments.length }}</span>
          </button>
          <button class="tab-btn" :class="{ active: activeTab === 'pending' }" @click="activeTab = 'pending'">
            Pending <span class="tab-count pending">{{ pendingCount }}</span>
          </button>
          <button class="tab-btn" :class="{ active: activeTab === 'completed' }" @click="activeTab = 'completed'">
            Completed <span class="tab-count">{{ completedCount }}</span>
          </button>
          <button class="tab-btn" :class="{ active: activeTab === 'refunded' }" @click="activeTab = 'refunded'">
            Refunded <span class="tab-count refunded">{{ refundedCount }}</span>
          </button>
        </div>

        <!-- Filters -->
        <div class="filter-bar">
          <div class="filter-left">
            <div class="search-box">
              <span class="search-icon">🔍</span>
              <input 
                v-model="searchQuery" 
                type="text" 
                class="search-input" 
                placeholder="Search by user, invoice or transaction ID..."
                @input="onSearchInput"
              />
              <button v-if="searchQuery" class="search-clear" @click="clearSearch">×</button>
            </div>
            <select v-model="filterType" class="filter-select">
              <option value="all">All Types</option>
              <option value="school">School</option>
              <option value="consultant">Consultant</option>
              <option value="personal">Personal</option>
              <option value="business">Business</option>
            </select>
            <select v-model="filterMethod" class="filter-select">
              <option value="all">All Methods</option>
              <option value="stripe">Stripe</option>
              <option value="bank_transfer">Bank Transfer</option>
              <option value="cheque">Cheque</option>
            </select>
          </div>
          <div class="filter-right">
            <span class="result-count">{{ filteredPayments.length }} payments</span>
          </div>
        </div>

        <!-- Payments Table -->
        <div class="users-table-wrapper">
          <table class="users-table">
            <thead>
              <tr>
                <th class="th-user">User</th>
                <th class="th-type">Type</th>
                <th class="th-amount">Amount</th>
                <th class="th-date">Date</th>
                <th class="th-method">Method</th>
                <th class="th-status">Status</th>
                <th class="th-invoice">Invoice</th>
                <th class="th-actions">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="payment in paginatedPayments" :key="payment.id" class="user-row">
                <td class="td-user">
                  <div class="user-info-cell">
                    <div class="user-details">
                      <span class="user-name">{{ payment.userName }}</span>
                      <span class="user-email">{{ payment.userEmail }}</span>
                    </div>
                  </div>
                </td>
                <td class="td-type">
                  <span class="type-badge" :class="'type-' + payment.userType">{{ payment.userType }}</span>
                </td>
                <td class="td-amount">
                  <span class="amount-value">£{{ payment.amount.toFixed(2) }}</span>
                </td>
                <td class="td-date">{{ payment.date }}</td>
                <td class="td-method">
                  <span class="method-badge" :class="'method-' + payment.method">{{ formatMethod(payment.method) }}</span>
                </td>
                <td class="td-status">
                  <span class="status-badge" :class="'status-' + payment.status">{{ payment.status }}</span>
                </td>
                <td class="td-invoice">
                  <span class="invoice-code">{{ payment.invoiceNumber }}</span>
                </td>
                <td class="td-actions">
                  <div class="action-buttons">
                    <button class="btn-action btn-view" @click="viewPayment(payment.id)" title="View Details">
                      👁️
                    </button>
                    <button 
                      v-if="payment.status === 'pending'" 
                      class="btn-action btn-approve" 
                      @click="openApproveModal(payment)" 
                      title="Approve Payment"
                    >
                      ✅
                    </button>
                    <button 
                      v-if="payment.status === 'pending'" 
                      class="btn-action btn-reject" 
                      @click="openRejectModal(payment)" 
                      title="Reject Payment"
                    >
                      ❌
                    </button>
                    <button 
                      v-if="payment.status !== 'pending'" 
                      class="btn-action btn-download" 
                      @click="downloadInvoice(payment.invoiceNumber)" 
                      title="Download Invoice"
                    >
                      📥
                    </button>
                    <button 
                      v-if="payment.status === 'completed'" 
                      class="btn-action btn-refund" 
                      @click="openRefundModal(payment)" 
                      title="Refund"
                    >
                      ↩️
                    </button>
                  </div>
                </td>
              </tr>
              <tr v-if="paginatedPayments.length === 0">
                <td colspan="8" class="empty-state">
                  <div class="empty-content">
                    <span class="empty-icon">💳</span>
                    <p>No payments found</p>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Pagination -->
        <div class="pagination">
          <span class="pagination-info">
            Showing {{ paginationStart }}–{{ paginationEnd }} of {{ filteredPayments.length }} payments
          </span>
          <div class="pagination-controls">
            <button 
              class="btn-page" 
              :disabled="currentPage === 1" 
              @click="currentPage--"
            >← Previous</button>
            
            <button 
              v-for="page in visiblePages" 
              :key="page" 
              class="btn-page" 
              :class="{ active: page === currentPage }"
              @click="currentPage = page"
            >{{ page }}</button>
            
            <button 
              class="btn-page" 
              :disabled="currentPage === totalPages" 
              @click="currentPage++"
            >Next →</button>
          </div>
        </div>
      </main>
    </div>

    <!-- Payment Detail Panel -->
    <div v-if="showPanel && selectedPayment" class="detail-panel-overlay" @click.self="closePanel">
      <div class="detail-panel">
        <div class="panel-header">
          <h2 class="panel-title">Payment Details</h2>
          <button class="panel-close" @click="closePanel">×</button>
        </div>
        <div class="panel-body">
          <div class="panel-section">
            <h3 class="section-title">Transaction Information</h3>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">Transaction ID</span>
                <span class="info-value">{{ selectedPayment.id }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Invoice Number</span>
                <span class="info-value">{{ selectedPayment.invoiceNumber }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Amount</span>
                <span class="info-value amount-value">£{{ selectedPayment.amount.toFixed(2) }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Status</span>
                <span class="info-value">
                  <span class="status-badge" :class="'status-' + selectedPayment.status">{{ selectedPayment.status }}</span>
                </span>
              </div>
              <div class="info-item">
                <span class="info-label">Payment Method</span>
                <span class="info-value">{{ formatMethod(selectedPayment.method) }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Date</span>
                <span class="info-value">{{ selectedPayment.date }}</span>
              </div>
            </div>
          </div>
          <div class="panel-section">
            <h3 class="section-title">User Information</h3>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">Name</span>
                <span class="info-value">{{ selectedPayment.userName }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Email</span>
                <span class="info-value">{{ selectedPayment.userEmail }}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Type</span>
                <span class="info-value">
                  <span class="type-badge" :class="'type-' + selectedPayment.userType">{{ selectedPayment.userType }}</span>
                </span>
              </div>
            </div>
          </div>
        </div>
        <div class="panel-footer">
          <button class="btn btn-secondary" @click="closePanel">Close</button>
          <button class="btn btn-primary" @click="downloadInvoice(selectedPayment.invoiceNumber)">Download Invoice</button>
        </div>
      </div>
    </div>

    <!-- Refund Modal -->
    <div v-if="refundModal" class="modal-overlay" @click.self="closeRefundModal">
      <div class="modal-box">
        <div class="modal-header">
          <h2 class="modal-title">Process Refund</h2>
          <button class="modal-close" @click="closeRefundModal">×</button>
        </div>
        <div class="modal-body">
          <div class="refund-summary" v-if="refundPayment">
            <p>Refund payment for <strong>{{ refundPayment.userName }}</strong></p>
            <p class="refund-amount">Amount: £{{ refundPayment.amount.toFixed(2) }}</p>
          </div>
          <div class="form-group">
            <label class="form-label">Refund Reason <span class="required">*</span></label>
            <textarea v-model="refundReason" class="form-textarea" rows="3" placeholder="Enter reason for refund..."></textarea>
          </div>
          <div class="form-group">
            <label class="form-label">Refund Amount</label>
            <input v-model="refundAmount" type="number" step="0.01" class="form-input" />
            <span class="form-hint">Leave full amount for full refund</span>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" @click="closeRefundModal">Cancel</button>
          <button class="btn btn-danger" @click="processRefund">Confirm Refund</button>
        </div>
      </div>
    </div>
    <!-- Approve Modal -->
    <div v-if="approveModal" class="modal-overlay" @click.self="closeApproveModal">
      <div class="modal-box">
        <div class="modal-header">
          <h2 class="modal-title">Approve Payment</h2>
          <button class="modal-close" @click="closeApproveModal">×</button>
        </div>
        <div class="modal-body">
          <div class="refund-summary" v-if="approvePaymentItem">
            <p>Approve payment for <strong>{{ approvePaymentItem.userName }}</strong></p>
            <p class="refund-amount">£{{ approvePaymentItem.amount.toFixed(2) }}</p>
            <p style="margin-top: 0.5rem; color: #64748b; font-size: 0.875rem;">
              Invoice: {{ approvePaymentItem.invoiceNumber }} | Method: {{ formatMethod(approvePaymentItem.method) }}
            </p>
          </div>
          <p style="color: #374151; font-size: 0.9rem;">Are you sure you want to mark this payment as completed?</p>
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" @click="closeApproveModal">Cancel</button>
          <button class="btn btn-primary" @click="processApprove">Confirm Approve</button>
        </div>
      </div>
    </div>

    <!-- Reject Modal -->
    <div v-if="rejectModal" class="modal-overlay" @click.self="closeRejectModal">
      <div class="modal-box">
        <div class="modal-header">
          <h2 class="modal-title">Reject Payment</h2>
          <button class="modal-close" @click="closeRejectModal">×</button>
        </div>
        <div class="modal-body">
          <div class="refund-summary" v-if="rejectPaymentItem">
            <p>Reject payment for <strong>{{ rejectPaymentItem.userName }}</strong></p>
            <p class="refund-amount">£{{ rejectPaymentItem.amount.toFixed(2) }}</p>
          </div>
          <div class="form-group">
            <label class="form-label">Rejection Reason <span class="required">*</span></label>
            <textarea v-model="rejectReason" class="form-textarea" rows="3" placeholder="Enter reason for rejection..."></textarea>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" @click="closeRejectModal">Cancel</button>
          <button class="btn btn-danger" @click="processReject">Confirm Reject</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { setMeta } = useSEO()
setMeta({
  title: 'Payments — BSP Staff',
  description: 'Manage payments, invoices and refunds',
  path: '/BSP/payments',
  type: 'website'
})

// Mock payments data
const payments = ref([
  { id: 'pi_3O9xK2L9d2fG4hJk', userName: 'Westminster Academy', userEmail: 'finance@westminster.edu', userType: 'school', amount: 2500.00, date: '2026-04-28', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-001' },
  { id: 'pi_4P0yL3M0e3gH5iKl', userName: 'Sarah Chen', userEmail: 'sarah@chen-consulting.com', userType: 'consultant', amount: 299.00, date: '2026-04-27', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-002' },
  { id: 'pi_5Q1zM4N1f4hI6jLm', userName: 'Greenwich School', userEmail: 'accounts@greenwich.edu.hk', userType: 'school', amount: 2500.00, date: '2026-04-26', method: 'bank_transfer', status: 'pending', invoiceNumber: 'INV-2026-003' },
  { id: 'pi_6R2aN5O2g5iJ7kMn', userName: 'John Smith', userEmail: 'john@smith-edu.co.uk', userType: 'consultant', amount: 299.00, date: '2026-04-25', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-004' },
  { id: 'pi_7S3bO6P3h6jK8lNo', userName: 'Apex Business Ltd', userEmail: 'accounts@apexbiz.com', userType: 'business', amount: 1200.00, date: '2026-04-24', method: 'cheque', status: 'pending', invoiceNumber: 'INV-2026-005' },
  { id: 'pi_8T4cP7Q4i7kL9mOp', userName: 'Emily Zhang', userEmail: 'emily@zhang-edu.cn', userType: 'personal', amount: 99.00, date: '2026-04-23', method: 'stripe', status: 'refunded', invoiceNumber: 'INV-2026-006' },
  { id: 'pi_9U5dQ8R5j8lM0nPq', userName: 'Harrow International', userEmail: 'bursar@harrow.hk', userType: 'school', amount: 2500.00, date: '2026-04-22', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-007' },
  { id: 'pi_0V6eR9S6k9mN1oQr', userName: 'Michael Brown', userEmail: 'michael@brown-consulting.com', userType: 'consultant', amount: 299.00, date: '2026-04-21', method: 'bank_transfer', status: 'completed', invoiceNumber: 'INV-2026-008' },
  { id: 'pi_1W7fS0T7l0nO2pRs', userName: 'Dubai British School', userEmail: 'finance@dubaibritish.ae', userType: 'school', amount: 2500.00, date: '2026-04-20', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-009' },
  { id: 'pi_2X8gT1U8m1oP3qSt', userName: 'Global Study Partners', userEmail: 'accounts@globalstudy.hk', userType: 'business', amount: 1200.00, date: '2026-04-19', method: 'stripe', status: 'pending', invoiceNumber: 'INV-2026-010' },
  { id: 'pi_3Y9hU2V9n2pQ4rTu', userName: 'Crown Education', userEmail: 'finance@crownedu.com', userType: 'school', amount: 2500.00, date: '2026-04-18', method: 'stripe', status: 'completed', invoiceNumber: 'INV-2026-011' },
  { id: 'pi_4Z0iV3W0o3qR5sUv', userName: 'James Wong', userEmail: 'james@wong-consulting.sg', userType: 'consultant', amount: 299.00, date: '2026-04-17', method: 'cheque', status: 'completed', invoiceNumber: 'INV-2026-012' },
])

// State
const activeTab = ref<'all' | 'pending' | 'completed' | 'refunded'>('all')
const searchQuery = ref('')
const filterType = ref('all')
const filterMethod = ref('all')
const currentPage = ref(1)
const pageSize = 10
const showPanel = ref(false)
const selectedPayment = ref<any>(null)
const refundModal = ref(false)
const refundPayment = ref<any>(null)
const refundReason = ref('')
const refundAmount = ref(0)
const approveModal = ref(false)
const approvePaymentItem = ref<any>(null)
const rejectModal = ref(false)
const rejectPaymentItem = ref<any>(null)
const rejectReason = ref('')

// Debounce search
let searchTimeout: ReturnType<typeof setTimeout> | null = null
const debouncedQuery = ref('')

const onSearchInput = () => {
  if (searchTimeout) clearTimeout(searchTimeout)
  searchTimeout = setTimeout(() => {
    debouncedQuery.value = searchQuery.value
    currentPage.value = 1
  }, 300)
}

const clearSearch = () => {
  searchQuery.value = ''
  debouncedQuery.value = ''
  currentPage.value = 1
}

// Computed
const filteredPayments = computed(() => {
  let result = payments.value

  // Tab filter
  if (activeTab.value !== 'all') {
    result = result.filter(p => p.status === activeTab.value)
  }

  // Type filter
  if (filterType.value !== 'all') {
    result = result.filter(p => p.userType === filterType.value)
  }

  // Method filter
  if (filterMethod.value !== 'all') {
    result = result.filter(p => p.method === filterMethod.value)
  }

  // Search
  if (debouncedQuery.value) {
    const q = debouncedQuery.value.toLowerCase()
    result = result.filter(p =>
      p.userName.toLowerCase().includes(q) ||
      p.userEmail.toLowerCase().includes(q) ||
      p.invoiceNumber.toLowerCase().includes(q) ||
      p.id.toLowerCase().includes(q)
    )
  }

  return result
})

const paginatedPayments = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  return filteredPayments.value.slice(start, start + pageSize)
})

const totalPages = computed(() => Math.ceil(filteredPayments.value.length / pageSize))

const paginationStart = computed(() => {
  if (filteredPayments.value.length === 0) return 0
  return (currentPage.value - 1) * pageSize + 1
})

const paginationEnd = computed(() => {
  return Math.min(currentPage.value * pageSize, filteredPayments.value.length)
})

const visiblePages = computed(() => {
  const total = totalPages.value
  if (total <= 5) return Array.from({ length: total }, (_, i) => i + 1)
  if (currentPage.value <= 3) return [1, 2, 3, 4, 5]
  if (currentPage.value >= total - 2) return [total - 4, total - 3, total - 2, total - 1, total]
  return [currentPage.value - 2, currentPage.value - 1, currentPage.value, currentPage.value + 1, currentPage.value + 2]
})

const totalRevenue = computed(() =>
  payments.value.filter(p => p.status === 'completed').reduce((sum, p) => sum + p.amount, 0)
)

const pendingCount = computed(() => payments.value.filter(p => p.status === 'pending').length)
const completedCount = computed(() => payments.value.filter(p => p.status === 'completed').length)
const refundedCount = computed(() => payments.value.filter(p => p.status === 'refunded').length)

// Methods
const formatMethod = (method: string) => {
  const map: Record<string, string> = {
    stripe: 'Stripe',
    bank_transfer: 'Bank Transfer',
    cheque: 'Cheque'
  }
  return map[method] || method
}

const viewPayment = (id: string) => {
  selectedPayment.value = payments.value.find(p => p.id === id) || null
  showPanel.value = true
}

const closePanel = () => {
  showPanel.value = false
  selectedPayment.value = null
}

const downloadInvoice = (invoiceNumber: string) => {
  alert(`Downloading invoice ${invoiceNumber}...`)
}

const openRefundModal = (payment: any) => {
  refundPayment.value = payment
  refundAmount.value = payment.amount
  refundReason.value = ''
  refundModal.value = true
}

const closeRefundModal = () => {
  refundModal.value = false
  refundPayment.value = null
  refundReason.value = ''
  refundAmount.value = 0
}

const processRefund = () => {
  if (!refundPayment.value || !refundReason.value) return
  const payment = payments.value.find(p => p.id === refundPayment.value.id)
  if (payment) {
    payment.status = 'refunded'
  }
  closeRefundModal()
  alert('Refund processed successfully')
}

const openApproveModal = (payment: any) => {
  approvePaymentItem.value = payment
  approveModal.value = true
}

const closeApproveModal = () => {
  approveModal.value = false
  approvePaymentItem.value = null
}

const processApprove = () => {
  if (!approvePaymentItem.value) return
  const payment = payments.value.find(p => p.id === approvePaymentItem.value.id)
  if (payment) {
    payment.status = 'completed'
  }
  closeApproveModal()
  alert('Payment approved successfully')
}

const openRejectModal = (payment: any) => {
  rejectPaymentItem.value = payment
  rejectReason.value = ''
  rejectModal.value = true
}

const closeRejectModal = () => {
  rejectModal.value = false
  rejectPaymentItem.value = null
  rejectReason.value = ''
}

const processReject = () => {
  if (!rejectPaymentItem.value || !rejectReason.value) return
  const payment = payments.value.find(p => p.id === rejectPaymentItem.value.id)
  if (payment) {
    payment.status = 'refunded'
  }
  closeRejectModal()
  alert('Payment rejected')
}

const exportPayments = () => {
  alert('Exporting payments CSV...')
}

// Reset page on tab change
watch(activeTab, () => {
  currentPage.value = 1
})
</script>

<style scoped>
.bsp-dashboard {
  min-height: 100vh;
  background: var(--bsp-light, #f8fafc);
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: var(--bsp-dark, #1e293b);
}

.dashboard-body {
  display: flex;
  min-height: 100vh;
}

/* Sidebar */
.sidebar {
  width: 260px;
  background: white;
  border-right: 1px solid #e2e8f0;
  padding: 1.5rem 0;
  flex-shrink: 0;
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
}

.nav-section {
  margin-bottom: 1.5rem;
}

.nav-section-title {
  font-size: 0.7rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #94a3b8;
  padding: 0 1.5rem;
  margin-bottom: 0.5rem;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.875rem 1.5rem;
  color: #64748b;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.9rem;
  transition: all 0.2s;
  border-left: 3px solid transparent;
}

.nav-item:hover {
  background: #f1f5f9;
  color: var(--bsp-primary, #212E54);
}

.nav-item.active {
  background: #eff6ff;
  color: var(--bsp-primary, #212E54);
  border-left-color: var(--bsp-secondary, #3b82f6);
  font-weight: 600;
}

.nav-icon { font-size: 1.1rem; }

.nav-count {
  margin-left: auto;
  background: #f1f5f9;
  color: #64748b;
  font-size: 0.7rem;
  font-weight: 600;
  padding: 0.15rem 0.5rem;
  border-radius: 4px;
}

/* Main Content */
.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
  overflow-y: auto;
}

/* Page Header */
.page-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1.5rem;
}

.page-title {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--bsp-primary, #212E54);
  margin-bottom: 0.25rem;
}

.page-subtitle {
  font-size: 0.9rem;
  color: #64748b;
}

.page-actions {
  display: flex;
  gap: 0.75rem;
}

/* Buttons */
.btn {
  padding: 0.625rem 1.25rem;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  cursor: pointer;
  border: none;
  transition: all 0.2s;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
}

.btn-primary {
  background: var(--bsp-secondary, #3b82f6);
  color: white;
}

.btn-primary:hover { background: #2563eb; }

.btn-secondary {
  background: white;
  color: #374151;
  border: 1px solid #e2e8f0;
}

.btn-secondary:hover { background: #f9fafb; }

.btn-danger {
  background: #ef4444;
  color: white;
}

.btn-danger:hover { background: #dc2626; }

/* Stats Grid */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: white;
  border-radius: 10px;
  padding: 1.25rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
}

.stat-card-highlight {
  background: linear-gradient(135deg, #10b981, #059669);
  color: white;
}

.stat-card-highlight .stat-number,
.stat-card-highlight .stat-label { color: white; }

.stat-icon {
  font-size: 1.75rem;
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f1f5f9;
  border-radius: 10px;
}

.stat-card-highlight .stat-icon { background: rgba(255,255,255,0.2); }

.stat-number {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--bsp-primary, #212E54);
  line-height: 1;
}

.stat-label {
  font-size: 0.8rem;
  color: #64748b;
  margin-top: 0.25rem;
}

/* Tabs */
.tab-bar {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.25rem;
  border-bottom: 1px solid #e2e8f0;
  padding-bottom: 0.5rem;
}

.tab-btn {
  padding: 0.625rem 1rem;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  color: #64748b;
  background: transparent;
  border: none;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.tab-btn:hover {
  background: #f1f5f9;
  color: #374151;
}

.tab-btn.active {
  background: #eff6ff;
  color: var(--bsp-secondary, #3b82f6);
}

.tab-count {
  background: #f1f5f9;
  color: #64748b;
  font-size: 0.75rem;
  font-weight: 600;
  padding: 0.125rem 0.5rem;
  border-radius: 9999px;
}

.tab-count.pending { background: #fef3c7; color: #d97706; }
.tab-count.refunded { background: #fee2e2; color: #dc2626; }

/* Filter Bar */
.filter-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.25rem;
  gap: 1rem;
}

.filter-left {
  display: flex;
  gap: 0.75rem;
  align-items: center;
  flex: 1;
}

.filter-right {
  flex-shrink: 0;
}

.search-box {
  position: relative;
  flex: 1;
  max-width: 360px;
}

.search-icon {
  position: absolute;
  left: 0.875rem;
  top: 50%;
  transform: translateY(-50%);
  font-size: 0.9rem;
  color: #94a3b8;
}

.search-input {
  width: 100%;
  padding: 0.625rem 2.5rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.875rem;
  background: white;
}

.search-input:focus {
  outline: none;
  border-color: var(--bsp-secondary, #3b82f6);
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.search-clear {
  position: absolute;
  right: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  font-size: 1.1rem;
  color: #94a3b8;
  cursor: pointer;
}

.filter-select {
  padding: 0.625rem 2rem 0.625rem 0.875rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.875rem;
  background: white;
  cursor: pointer;
}

.result-count {
  font-size: 0.875rem;
  color: #64748b;
  font-weight: 500;
}

/* Table */
.users-table-wrapper {
  background: white;
  border-radius: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  overflow: hidden;
  margin-bottom: 1.25rem;
}

.users-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.875rem;
}

.users-table th {
  text-align: left;
  padding: 0.875rem 1rem;
  font-weight: 600;
  color: #64748b;
  border-bottom: 1px solid #e2e8f0;
  white-space: nowrap;
}

.users-table td {
  padding: 1rem;
  border-bottom: 1px solid #f1f5f9;
  vertical-align: middle;
}

.user-row:hover { background: #f8fafc; }

.td-user { min-width: 200px; }

.user-info-cell {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.user-details {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.user-name {
  font-weight: 600;
  color: var(--bsp-dark, #1e293b);
}

.user-email {
  font-size: 0.8rem;
  color: #94a3b8;
}

.amount-value {
  font-weight: 700;
  color: var(--bsp-dark, #1e293b);
  font-size: 0.95rem;
}

/* Badges */
.type-badge,
.method-badge,
.status-badge {
  display: inline-flex;
  align-items: center;
  padding: 0.25rem 0.625rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: capitalize;
}

.type-school { background: #eff6ff; color: #2563eb; }
.type-consultant { background: #f0fdf4; color: #16a34a; }
.type-personal { background: #fef3c7; color: #d97706; }
.type-business { background: #f3e8ff; color: #9333ea; }

.method-stripe { background: #eff6ff; color: #2563eb; }
.method-bank_transfer { background: #f0fdf4; color: #16a34a; }
.method-cheque { background: #fef3c7; color: #d97706; }

.status-pending { background: #fef3c7; color: #d97706; }
.status-completed { background: #dcfce7; color: #16a34a; }
.status-refunded { background: #fee2e2; color: #dc2626; }

.invoice-code {
  font-family: monospace;
  font-size: 0.8rem;
  color: #64748b;
}

/* Actions */
.action-buttons {
  display: flex;
  gap: 0.375rem;
}

.btn-action {
  width: 32px;
  height: 32px;
  border-radius: 6px;
  border: 1px solid #e2e8f0;
  background: white;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.9rem;
  transition: all 0.2s;
}

.btn-action:hover { background: #f1f5f9; }

.btn-approve:hover { background: #dcfce7; border-color: #86efac; }

.btn-reject:hover { background: #fee2e2; border-color: #fca5a5; }

.btn-refund:hover { background: #fee2e2; border-color: #fca5a5; }

/* Empty State */
.empty-state {
  text-align: center;
  padding: 3rem 1rem;
  color: #94a3b8;
}

.empty-icon {
  font-size: 2.5rem;
  display: block;
  margin-bottom: 0.5rem;
}

/* Pagination */
.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
}

.pagination-info {
  font-size: 0.875rem;
  color: #64748b;
}

.pagination-controls {
  display: flex;
  gap: 0.375rem;
}

.btn-page {
  padding: 0.5rem 0.875rem;
  border-radius: 8px;
  border: 1px solid #e2e8f0;
  background: white;
  color: #374151;
  font-weight: 500;
  font-size: 0.875rem;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-page:hover:not(:disabled) {
  background: #f1f5f9;
}

.btn-page.active {
  background: var(--bsp-secondary, #3b82f6);
  color: white;
  border-color: var(--bsp-secondary, #3b82f6);
}

.btn-page:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Detail Panel Overlay */
.detail-panel-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  justify-content: flex-end;
}

.detail-panel {
  width: 480px;
  background: white;
  height: 100vh;
  overflow-y: auto;
  box-shadow: -4px 0 24px rgba(0, 0, 0, 0.15);
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.panel-title {
  font-size: 1.125rem;
  font-weight: 600;
  color: var(--bsp-dark, #1e293b);
}

.panel-close {
  background: none;
  border: none;
  font-size: 1.5rem;
  color: #94a3b8;
  cursor: pointer;
}

.panel-body {
  padding: 1.5rem;
}

.panel-section {
  margin-bottom: 1.5rem;
}

.section-title {
  font-size: 0.875rem;
  font-weight: 600;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.75rem;
}

.info-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.info-label {
  font-size: 0.75rem;
  color: #94a3b8;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.info-value {
  font-weight: 600;
  color: var(--bsp-dark, #1e293b);
  font-size: 0.9rem;
}

.panel-footer {
  display: flex;
  gap: 0.75rem;
  padding: 1.25rem 1.5rem;
  border-top: 1px solid #e2e8f0;
}

/* Modal */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-box {
  background: white;
  border-radius: 12px;
  width: 480px;
  max-width: 90vw;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.2);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.modal-title {
  font-size: 1.125rem;
  font-weight: 600;
}

.modal-close {
  background: none;
  border: none;
  font-size: 1.5rem;
  color: #94a3b8;
  cursor: pointer;
}

.modal-body {
  padding: 1.5rem;
}

.refund-summary {
  background: #f8fafc;
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 1rem;
}

.refund-amount {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--bsp-primary, #212E54);
  margin-top: 0.5rem;
}

.form-group {
  margin-bottom: 1rem;
}

.form-label {
  display: block;
  font-size: 0.875rem;
  font-weight: 500;
  color: #374151;
  margin-bottom: 0.375rem;
}

.required { color: #ef4444; }

.form-input,
.form-textarea {
  width: 100%;
  padding: 0.625rem 0.875rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.875rem;
  background: white;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: var(--bsp-secondary, #3b82f6);
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.form-hint {
  font-size: 0.8rem;
  color: #94a3b8;
  margin-top: 0.25rem;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 0.75rem;
  padding: 1rem 1.5rem;
  border-top: 1px solid #e2e8f0;
}

/* Responsive */
@media (max-width: 1024px) {
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .sidebar {
    display: none;
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }

  .filter-left {
    flex-wrap: wrap;
  }

  .detail-panel {
    width: 100%;
  }
}
</style>