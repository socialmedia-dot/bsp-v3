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
            <NuxtLink to="/BSP/users/bspstaff" class="nav-item active">
              <span class="nav-icon">👔</span>
              <span>BSP Staff</span>
              <span class="nav-count">{{ staff.length }}</span>
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
            <h1 class="page-title">👔 BSP Staff</h1>
            <p class="page-subtitle">Manage BSP staff accounts, roles and permissions</p>
          </div>
          <div class="page-actions">
            <button class="btn btn-secondary" @click="exportStaff">📥 Export CSV</button>
            <button class="btn btn-primary" @click="openAddModal">➕ Add Staff</button>
          </div>
        </div>

        <!-- Tabs -->
        <div class="tab-bar">
          <button class="tab-btn" :class="{ active: activeTab === 'active' }" @click="activeTab = 'active'">
            Active Staff <span class="tab-count">{{ staff.length }}</span>
          </button>
          <button class="tab-btn" :class="{ active: activeTab === 'deleted' }" @click="activeTab = 'deleted'">
            Deleted Accounts <span class="tab-count deleted">{{ deletedStaff.length }}</span>
          </button>
          <button class="tab-btn" :class="{ active: activeTab === 'roles' }" @click="activeTab = 'roles'">
            Role Permissions <span class="tab-count">{{ roles.length }}</span>
          </button>
        </div>

        <!-- Active Staff Tab -->
        <template v-if="activeTab === 'active'">
          <div class="filter-bar">
            <div class="filter-left">
              <div class="search-box">
                <span class="search-icon">🔍</span>
                <input v-model="searchQuery" type="text" class="search-input" placeholder="Search by name or email..." @input="onSearchInput" />
                <button v-if="searchQuery" class="search-clear" @click="clearSearch">×</button>
              </div>
              <select v-model="filterRole" class="filter-select">
                <option value="all">All Roles</option>
                <option value="master">Master</option>
                <option value="manager">Manager</option>
                <option value="staff">Staff</option>
              </select>
            </div>
            <div class="filter-right">
              <span class="result-count">{{ filteredStaff.length }} staff</span>
            </div>
          </div>

          <div class="users-table-wrapper">
            <table class="users-table">
              <thead>
                <tr>
                  <th class="th-user">Staff</th>
                  <th class="th-role">Role</th>
                  <th class="th-permissions">Permissions</th>
                  <th class="th-login">Last Login</th>
                  <th class="th-status">Status</th>
                  <th class="th-actions">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="member in paginatedStaff" :key="member.id" class="user-row">
                  <td class="td-user">
                    <div class="user-info-cell">
                      <div class="user-avatar">{{ getInitials(member.name) }}</div>
                      <div class="user-details">
                        <span class="user-name">{{ member.name }}</span>
                        <span class="user-email">{{ member.email }}</span>
                        <span class="user-code-inline">ID: {{ member.userCode }}</span>
                      </div>
                    </div>
                  </td>
                  <td class="td-role">
                    <span class="role-badge" :class="'role-' + member.role">{{ formatRole(member.role) }}</span>
                  </td>
                  <td class="td-permissions">
                    <div class="permission-badges">
                      <span v-for="perm in member.permissions.slice(0, 3)" :key="perm" class="perm-badge" :class="'perm-' + perm">{{ formatPerm(perm) }}</span>
                      <span v-if="member.permissions.length > 3" class="perm-more">+{{ member.permissions.length - 3 }}</span>
                    </div>
                  </td>
                  <td class="td-login"><span class="login-text">{{ member.lastLogin }}</span></td>
                  <td class="td-status">
                    <span class="status-badge" :class="'status-' + member.status">{{ formatStatus(member.status) }}</span>
                  </td>
                  <td class="td-actions">
                    <div class="action-buttons">
                      <button class="btn-action btn-view" @click="viewStaff(member.id)" title="View Details">👁️</button>
                      <button class="btn-action btn-edit" @click="editStaff(member.id)" title="Edit">✏️</button>
                      <button class="btn-action btn-more" @click="toggleMenu(member.id)" title="More">⋮</button>
                    </div>
                  </td>
                </tr>
                <tr v-if="paginatedStaff.length === 0">
                  <td colspan="6" class="empty-state">
                    <div class="empty-content"><span class="empty-icon">👔</span><p>No staff found</p></div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <div class="pagination">
            <span class="pagination-info">Showing {{ paginationStart }}–{{ paginationEnd }} of {{ filteredStaff.length }} staff</span>
            <div class="pagination-controls">
              <button class="btn-page" :disabled="currentPage === 1" @click="currentPage--">← Previous</button>
              <button v-for="page in visiblePages" :key="page" class="btn-page" :class="{ active: page === currentPage }" @click="currentPage = page">{{ page }}</button>
              <button class="btn-page" :disabled="currentPage === totalPages" @click="currentPage++">Next →</button>
            </div>
          </div>
        </template>

        <!-- Deleted Accounts Tab -->
        <template v-if="activeTab === 'deleted'">
          <div class="users-table-wrapper">
            <table class="users-table">
              <thead>
                <tr>
                  <th class="th-user">Staff</th>
                  <th class="th-deletedat">Deleted At</th>
                  <th class="th-deletedby">Deleted By</th>
                  <th class="th-reason">Reason</th>
                  <th class="th-evidence">Evidence</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="record in deletedStaff" :key="record.userCode" class="user-row deleted-row">
                  <td class="td-user">
                    <div class="user-info-cell">
                      <div class="user-avatar deleted-avatar">{{ getInitials(record.name) }}</div>
                      <div class="user-details">
                        <span class="user-name">{{ record.name }}</span>
                        <span class="user-email">{{ record.email }}</span>
                        <span class="user-code-inline deleted-code">ID: {{ record.userCode }}</span>
                      </div>
                    </div>
                  </td>
                  <td class="td-deletedat">{{ record.deletedAt }}</td>
                  <td class="td-deletedby">{{ record.deletedBy }}</td>
                  <td class="td-reason"><span class="reason-text" :title="record.reason">{{ record.reason.length > 30 ? record.reason.slice(0, 30) + '...' : record.reason }}</span></td>
                  <td class="td-evidence">
                    <span v-if="record.evidenceFile" class="evidence-file">📎 {{ record.evidenceFile }}</span>
                    <span v-else class="no-evidence">—</span>
                  </td>
                </tr>
              </tbody>
            </table>
            <div v-if="deletedStaff.length === 0" class="empty-state">No deleted accounts</div>
          </div>
        </template>

        <!-- Role Permissions Tab -->
        <template v-if="activeTab === 'roles'">
          <div class="settings-section">
            <div class="section-header-row">
              <div>
                <h2 class="section-title">Role Permissions Overview</h2>
                <p class="section-desc">These are the default permission templates applied when creating new staff accounts.</p>
              </div>
              <button class="btn btn-secondary" @click="openAddRoleModal">➕ Add Role</button>
            </div>
            <div class="permissions-grid">
              <div v-for="role in roles" :key="role.key" class="permission-card">
                <div class="permission-header">
                  <div class="permission-title-row">
                    <span class="role-icon">{{ role.icon }}</span>
                    <h3 class="permission-title">{{ role.name }}</h3>
                    <button v-if="role.key !== 'master'" class="btn-edit-role" @click="openEditRoleModal(role)" title="Edit Role">✏️</button>
                  </div>
                  <span class="permission-count">{{ role.permissions.length }} permissions</span>
                </div>
                <ul class="permission-list">
                  <li v-for="perm in role.permissions" :key="perm">{{ perm }}</li>
                </ul>
                <div class="permission-footer">
                  <span class="staff-count">{{ staff.filter(s => s.role === role.key).length }} staff assigned</span>
                </div>
              </div>
            </div>
          </div>

          <div class="settings-section">
            <div class="section-header-row">
              <div>
                <h2 class="section-title">Permission Matrix</h2>
                <p class="section-desc">{{ roleEditMode ? 'Click checkboxes to toggle permissions, then Save Changes.' : 'Quick reference for which roles can access which features.' }}</p>
              </div>
              <div class="section-actions">
                <button v-if="!roleEditMode" class="btn btn-secondary" @click="roleEditMode = true">✏️ Edit Permissions</button>
                <template v-else>
                  <button class="btn btn-secondary" @click="cancelRoleEdits">Cancel</button>
                  <button class="btn btn-primary" @click="saveRoleEdits">💾 Save Changes</button>
                </template>
              </div>
            </div>
            <div class="matrix-table-wrapper">
              <table class="matrix-table">
                <thead>
                  <tr>
                    <th>Permission</th>
                    <th v-for="role in roles" :key="role.key" class="matrix-role-header">
                      <span class="role-icon-small">{{ role.icon }}</span>
                      {{ role.name }}
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="perm in allPermissionLabels" :key="perm.key">
                    <td class="matrix-perm-name">{{ perm.label }}</td>
                    <td v-for="role in roles" :key="role.key" class="matrix-check">
                      <template v-if="roleEditMode">
                        <label class="matrix-checkbox">
                          <input
                            type="checkbox"
                            :checked="role.permissions.includes(perm.label)"
                            @change="toggleRolePermission(role.key, perm.label)"
                          />
                          <span class="checkbox-custom" :class="{ checked: role.permissions.includes(perm.label) }">✓</span>
                        </label>
                      </template>
                      <template v-else>
                        <span v-if="role.permissions.includes(perm.label)" class="check-yes">✓</span>
                        <span v-else class="check-no">—</span>
                      </template>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </template>
      </main>
    </div>

    <!-- Right Side Detail Panel -->
    <div v-if="showPanel" class="detail-panel-overlay" @click.self="closePanel">
      <div class="detail-panel">
        <div class="panel-header">
          <h2 class="panel-title">Staff Details</h2>
          <button class="panel-close" @click="closePanel">×</button>
        </div>
        <div v-if="selectedStaff" class="panel-body">
          <div class="panel-section">
            <h3 class="section-title">Staff Information</h3>
            <div class="info-grid">
              <div class="info-item full-width"><span class="info-label">Name</span><span class="info-value">{{ selectedStaff.name }}</span></div>
              <div class="info-item full-width"><span class="info-label">Email</span><span class="info-value">{{ selectedStaff.email }}</span></div>
              <div class="info-item"><span class="info-label">Staff ID</span><span class="info-value">{{ selectedStaff.id }}</span></div>
              <div class="info-item"><span class="info-label">Role</span><span class="info-value"><span class="role-badge" :class="'role-' + selectedStaff.role">{{ formatRole(selectedStaff.role) }}</span></span></div>
              <div class="info-item full-width">
                <span class="info-label">Permissions</span>
                <div class="panel-permissions-list">
                  <span v-for="perm in selectedStaff.permissions" :key="perm" class="panel-perm-badge" :class="'panel-perm-' + perm">{{ formatPermFull(perm) }}</span>
                  <span v-if="!selectedStaff.permissions || selectedStaff.permissions.length === 0" class="no-permissions">No permissions assigned</span>
                </div>
              </div>
              <div class="info-item"><span class="info-label">Registered</span><span class="info-value">{{ selectedStaff.registeredAt }}</span></div>
              <div class="info-item"><span class="info-label">Last Login</span><span class="info-value">{{ selectedStaff.lastLogin }}</span></div>
              <div class="info-item full-width"><span class="info-label">Status</span><span class="info-value"><span class="status-badge" :class="'status-' + selectedStaff.status">{{ formatStatus(selectedStaff.status) }}</span></span></div>
            </div>
          </div>
        </div>
        <div class="panel-footer">
          <button class="btn btn-secondary" @click="closePanel">Close</button>
          <button class="btn btn-primary" @click="editStaff(selectedStaff?.id)">Edit</button>
        </div>
      </div>
    </div>

    <!-- Edit Staff Modal -->
    <div v-if="editModal" class="modal-overlay" @click.self="closeEditModal">
      <div class="modal-box">
        <div class="modal-header"><h2 class="modal-title">Edit Staff</h2><button class="modal-close" @click="closeEditModal">×</button></div>
        <div class="modal-body">
          <div class="form-group"><label class="form-label">Name</label><input v-model="editForm.name" type="text" class="form-input" /></div>
          <div class="form-group"><label class="form-label">Email</label><input v-model="editForm.email" type="email" class="form-input" /></div>
          <div class="form-group">
            <label class="form-label">Role</label>
            <select v-model="editForm.role" class="form-input">
              <option value="master">Master (Owner)</option>
              <option value="manager">Manager</option>
              <option value="staff">Staff</option>
            </select>
          </div>
          <div class="form-group"><label class="form-label">New Password <span class="label-hint">(leave blank to keep current)</span></label><input v-model="editForm.password" type="password" class="form-input" placeholder="Enter new password" /></div>
          <div class="modal-divider"></div>
          <div class="permissions-section">
            <div class="permissions-header">
              <span class="permissions-title">Access Permissions</span>
              <button class="btn-link" @click="toggleAllPermissions" type="button">{{ allPermissionsTicked ? 'Untick All' : 'Tick All' }}</button>
            </div>
            <div class="permissions-grid">
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="editUsers" /><span class="permission-label">✏️ Edit Users</span><span class="permission-desc">Add, edit user accounts</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="suspendUsers" /><span class="permission-label">⏸️ Suspend Users</span><span class="permission-desc">Suspend / reactivate accounts</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="resetPasswords" /><span class="permission-label">🔑 Reset Passwords</span><span class="permission-desc">Reset user passwords</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="approveAccounts" /><span class="permission-label">✅ Approve Accounts</span><span class="permission-desc">Approve new school/consultant accounts</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="viewCommission" /><span class="permission-label">💰 View Commission</span><span class="permission-desc">View commission data</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="updateCommission" /><span class="permission-label">📝 Update Commission</span><span class="permission-desc">Edit commission rates</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="exportData" /><span class="permission-label">📥 Export Data</span><span class="permission-desc">Download CSV reports</span></label>
              <label class="permission-item"><input type="checkbox" v-model="editForm.permissions" value="manageStaff" /><span class="permission-label">👔 Manage Staff</span><span class="permission-desc">Add / edit staff accounts</span></label>
              <label class="permission-item permission-danger"><input type="checkbox" v-model="editForm.permissions" value="deleteStaff" /><span class="permission-label">🗑️ Delete Staff</span><span class="permission-desc">Delete staff accounts</span></label>
            </div>
            <p class="permissions-note">💡 Tip: Master (Owner) has all permissions by default</p>
          </div>
          <div class="modal-divider"></div>
          <div class="form-group"><label class="form-label">Account Status</label><div class="status-display"><span class="status-badge" :class="'status-' + (editForm.status || 'active')">{{ formatStatus(editForm.status || 'active') }}</span></div></div>
          <div class="action-buttons">
            <button class="btn-action-text" @click="suspendUser" v-if="editForm.status !== 'suspended'"><span class="btn-icon">⏸</span> Suspend Account</button>
            <button class="btn-action-text btn-success" @click="unsuspendUser" v-if="editForm.status === 'suspended'"><span class="btn-icon">▶</span> Unsuspend Account</button>
            <button class="btn-action-text btn-danger" @click="openDeleteConfirm"><span class="btn-icon">🗑</span> Delete Account</button>
          </div>
        </div>
        <div class="modal-footer"><button class="btn btn-secondary" @click="closeEditModal">Cancel</button><button class="btn btn-primary" @click="saveEdit">Save Changes</button></div>
      </div>
    </div>

    <!-- Add Staff Modal -->
    <div v-if="addModal" class="modal-overlay" @click.self="closeAddModal">
      <div class="modal-box">
        <div class="modal-header"><h2 class="modal-title">Add Staff Member</h2><button class="modal-close" @click="closeAddModal">×</button></div>
        <div class="modal-body">
          <div class="form-group"><label class="form-label">Name <span class="required">*</span></label><input v-model="addForm.name" type="text" class="form-input" placeholder="Enter full name" /></div>
          <div class="form-group"><label class="form-label">Email <span class="required">*</span></label><input v-model="addForm.email" type="email" class="form-input" placeholder="staff@example.com" /></div>
          <div class="form-group"><label class="form-label">Role <span class="required">*</span></label>
            <select v-model="addForm.role" class="form-input">
              <option value="master">Master (Owner)</option>
              <option value="manager">Manager</option>
              <option value="staff">Staff</option>
            </select>
          </div>
          <div class="form-group"><label class="form-label">Password <span class="required">*</span></label><input v-model="addForm.password" type="password" class="form-input" placeholder="Enter initial password" /></div>
          <div class="modal-divider"></div>
          <div class="permissions-section">
            <div class="permissions-header"><span class="permissions-title">Access Permissions</span></div>
            <div class="permissions-grid">
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="editUsers" /><span class="permission-label">✏️ Edit Users</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="suspendUsers" /><span class="permission-label">⏸️ Suspend Users</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="resetPasswords" /><span class="permission-label">🔑 Reset Passwords</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="approveAccounts" /><span class="permission-label">✅ Approve Accounts</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="viewCommission" /><span class="permission-label">💰 View Commission</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="updateCommission" /><span class="permission-label">📝 Update Commission</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="exportData" /><span class="permission-label">📥 Export Data</span></label>
              <label class="permission-item"><input type="checkbox" v-model="addForm.permissions" value="manageStaff" /><span class="permission-label">👔 Manage Staff</span></label>
              <label class="permission-item permission-danger"><input type="checkbox" v-model="addForm.permissions" value="deleteStaff" /><span class="permission-label">🗑️ Delete Staff</span></label>
            </div>
            <p class="permissions-note">💡 Master (Owner) automatically has all permissions</p>
          </div>
        </div>
        <div class="modal-footer"><button class="btn btn-secondary" @click="closeAddModal">Cancel</button><button class="btn btn-primary" @click="addStaffMember">Add Staff</button></div>
      </div>
    </div>

    <!-- Delete Account Confirmation Modal -->
    <div v-if="deleteModal" class="modal-overlay" @click.self="closeDeleteModal">
      <div class="modal-box">
        <div class="modal-header"><h2 class="modal-title">Delete Account</h2><button class="modal-close" @click="closeDeleteModal">×</button></div>
        <div class="modal-body">
          <div class="delete-warning"><span class="warning-icon">⚠️</span><p>You are about to delete the account for <strong>{{ editForm.name }}</strong>. This action cannot be undone.</p></div>
          <div class="form-group"><label class="form-label">Reason for Deletion <span class="required">*</span></label><textarea v-model="deleteForm.reason" class="form-textarea" rows="3" placeholder="Enter reason for account deletion..."></textarea></div>
          <div class="form-group">
            <label class="form-label">Upload Evidence <span class="label-hint">(screenshot, email, etc.)</span></label>
            <div class="file-upload-area" @click="triggerFileUpload">
              <input ref="deleteFileInput" type="file" accept=".jpg,.jpeg,.png,.pdf" style="display:none" @change="handleDeleteFileChange" />
              <div v-if="!deleteForm.file" class="upload-placeholder"><span class="upload-icon">📎</span><span>Click to upload file</span><span class="upload-hint">JPG, PNG, PDF (max 5MB)</span></div>
              <div v-else class="file-selected"><span class="file-icon">📄</span><span class="file-name">{{ deleteForm.file.name }}</span><button class="btn-remove-file" @click.stop="removeDeleteFile">×</button></div>
            </div>
          </div>
          <div v-if="deleteForm.reason && !deleteForm.file" class="form-hint warning-hint">⚠️ Uploading evidence is recommended for compliance.</div>
        </div>
        <div class="modal-footer"><button class="btn btn-secondary" @click="closeDeleteModal">Cancel</button><button class="btn btn-danger" @click="confirmDeleteStaff">Delete Account</button></div>
      </div>
    </div>

    <!-- Add Role Modal -->
    <div v-if="addRoleModal" class="modal-overlay" @click.self="closeAddRoleModal">
      <div class="modal-box">
        <div class="modal-header"><h2 class="modal-title">Add New Role</h2><button class="modal-close" @click="closeAddRoleModal">×</button></div>
        <div class="modal-body">
          <div class="form-group"><label class="form-label">Role Name <span class="required">*</span></label><input v-model="addRoleForm.name" type="text" class="form-input" placeholder="e.g. Auditor, Support" /></div>
          <div class="form-group"><label class="form-label">Icon <span class="label-hint">(emoji)</span></label>
            <input v-model="addRoleForm.icon" type="text" class="form-input" placeholder="e.g. 🔧" maxlength="2" />
          </div>
          <div class="modal-divider"></div>
          <div class="permissions-section">
            <div class="permissions-header"><span class="permissions-title">Permissions</span></div>
            <div class="permissions-grid">
              <label v-for="perm in allPermissionLabels" :key="perm.key" class="permission-item">
                <input type="checkbox" v-model="addRoleForm.permissions" :value="perm.label" />
                <span class="permission-label">{{ perm.label }}</span>
              </label>
            </div>
          </div>
        </div>
        <div class="modal-footer"><button class="btn btn-secondary" @click="closeAddRoleModal">Cancel</button><button class="btn btn-primary" @click="confirmAddRole">Add Role</button></div>
      </div>
    </div>

    <!-- Edit Role Modal -->
    <div v-if="editRoleModal" class="modal-overlay" @click.self="closeEditRoleModal">
      <div class="modal-box">
        <div class="modal-header"><h2 class="modal-title">Edit Role: {{ editRoleForm.name }}</h2><button class="modal-close" @click="closeEditRoleModal">×</button></div>
        <div class="modal-body">
          <div class="form-group"><label class="form-label">Role Name <span class="required">*</span></label><input v-model="editRoleForm.name" type="text" class="form-input" /></div>
          <div class="form-group"><label class="form-label">Icon <span class="label-hint">(emoji)</span></label>
            <input v-model="editRoleForm.icon" type="text" class="form-input" placeholder="e.g. 🔧" maxlength="2" />
          </div>
          <div class="modal-divider"></div>
          <div class="permissions-section">
            <div class="permissions-header">
              <span class="permissions-title">Permissions</span>
              <button class="btn-link" @click="toggleAllRolePermissions" type="button">{{ allRolePermsTicked ? 'Untick All' : 'Tick All' }}</button>
            </div>
            <div class="permissions-grid">
              <label v-for="perm in allPermissionLabels" :key="perm.key" class="permission-item">
                <input type="checkbox" v-model="editRoleForm.permissions" :value="perm.label" />
                <span class="permission-label">{{ perm.label }}</span>
              </label>
            </div>
          </div>
          <div class="modal-divider"></div>
          <div class="delete-warning small-warning">
            <span class="warning-icon">⚠️</span>
            <p>Deleting this role will NOT affect existing staff with this role.</p>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-danger" @click="confirmDeleteRole">🗑️ Delete Role</button>
          <div style="flex:1"></div>
          <button class="btn btn-secondary" @click="closeEditRoleModal">Cancel</button>
          <button class="btn btn-primary" @click="confirmEditRole">Save Changes</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { setMeta } = useSEO()
setMeta({
  title: 'BSP Staff — BSP Dashboard',
  description: 'Manage BSP staff accounts, roles and permissions',
  path: '/BSP/users/bspstaff',
  type: 'website'
})

// Tabs
const activeTab = ref<'active' | 'deleted' | 'roles'>('active')

// Pagination state
const currentPage = ref(1)
const searchQuery = ref('')
const filterRole = ref('all')
const showPanel = ref(false)
const selectedStaff = ref<any>(null)
const editModal = ref(false)
const addModal = ref(false)
const editForm = ref({ name: '', email: '', role: 'staff', password: '', status: 'active', permissions: [] as string[] })
const addForm = ref({ name: '', email: '', role: 'staff', password: '', permissions: [] as string[] })
const deleteModal = ref(false)
const deleteForm = ref({ reason: '', file: null as File | null })
const deleteFileInput = ref<HTMLInputElement | null>(null)

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

// Page size based on screen width
const isMobile = ref(false)
const pageSize = computed(() => isMobile.value ? 20 : 30)

onMounted(() => {
  const checkMobile = () => { isMobile.value = window.innerWidth < 768 }
  checkMobile()
  window.addEventListener('resize', checkMobile)
})

// Helper functions
const getInitials = (name: string): string => {
  return name.split(' ').map(n => n[0]).join('').toUpperCase().slice(0, 2)
}

const formatRole = (role: string): string => {
  const roles: Record<string, string> = { master: 'Master', manager: 'Manager', staff: 'Staff' }
  return roles[role] || role
}

const ALL_PERMISSIONS = ['editUsers', 'suspendUsers', 'resetPasswords', 'approveAccounts', 'viewCommission', 'updateCommission', 'exportData', 'manageStaff', 'deleteStaff']

const allPermissionsTicked = computed(() => {
  return ALL_PERMISSIONS.every(p => editForm.value.permissions.includes(p))
})

const toggleAllPermissions = () => {
  if (allPermissionsTicked.value) {
    editForm.value.permissions = []
  } else {
    editForm.value.permissions = [...ALL_PERMISSIONS]
  }
}

const formatStatus = (status: string): string => {
  const statuses: Record<string, string> = { active: 'Active', suspended: 'Suspended', inactive: 'Inactive' }
  return statuses[status] || status
}

const formatPerm = (perm: string): string => {
  const map: Record<string, string> = {
    editUsers: 'Edit', suspendUsers: 'Suspend', resetPasswords: 'Reset', approveAccounts: 'Approve',
    viewCommission: 'View $', updateCommission: 'Update $', exportData: 'Export', manageStaff: 'Staff', deleteStaff: 'Delete'
  }
  return map[perm] || perm
}

const formatPermFull = (perm: string): string => {
  const map: Record<string, string> = {
    editUsers: '✏️ Edit Users', suspendUsers: '⏸️ Suspend Users', resetPasswords: '🔑 Reset Passwords',
    approveAccounts: '✅ Approve Accounts', viewCommission: '💰 View Commission', updateCommission: '📝 Update Commission',
    exportData: '📥 Export Data', manageStaff: '👔 Manage Staff', deleteStaff: '🗑️ Delete Staff'
  }
  return map[perm] || perm
}

// Mock data
const staff = ref([
  { id: 1, userCode: '2026040100001', name: 'Eric Tong (Owner)', email: 'eric@britishschoolportal.co.uk', role: 'master', permissions: ['editUsers', 'suspendUsers', 'resetPasswords', 'approveAccounts', 'viewCommission', 'updateCommission', 'exportData', 'manageStaff', 'deleteStaff'], registeredAt: '2025-01-01', lastLogin: '2026-04-22 03:30', status: 'active' },
  { id: 2, userCode: '2026041000001', name: 'Ms. Emily Chen', email: 'emily.chen@bsp.com', role: 'manager', permissions: ['editUsers', 'suspendUsers', 'resetPasswords', 'approveAccounts', 'exportData'], registeredAt: '2026-04-10', lastLogin: '2026-04-20 16:45', status: 'active' },
  { id: 3, userCode: '2026041500001', name: 'Mr. David Wong', email: 'david.wong@bsp.com', role: 'staff', permissions: ['resetPasswords'], registeredAt: '2026-04-15', lastLogin: '2026-04-19 14:20', status: 'active' },
  { id: 4, userCode: '2026041800001', name: 'Ms. Sarah Liu', email: 'sarah.liu@bsp.com', role: 'staff', permissions: ['resetPasswords'], registeredAt: '2026-04-18', lastLogin: '2026-04-18 11:00', status: 'active' }
])

const deletedStaff = ref([
  { userCode: '2026030100001', name: 'Mr. Michael Brown', email: 'michael.brown@bsp.com', deletedAt: '2026-03-15 10:00', deletedBy: 'Admin', reason: 'Staff resigned from position', evidenceFile: 'resignation-letter.pdf' }
])

const roles = ref([
  { key: 'master', name: 'Master (Owner)', icon: '👑', permissions: ['Full Access', 'Manage Staff', 'Financial Control', 'System Settings', 'User Management', 'Content Editing'] },
  { key: 'manager', name: 'Manager', icon: '📊', permissions: ['Manage Users', 'Approve Applications', 'View Payments', 'Edit Content', 'Export Reports'] },
  { key: 'staff', name: 'Staff', icon: '👤', permissions: ['Review Applications', 'View Users', 'Send Notifications', 'Reset Passwords'] }
])

const allPermissionLabels = [
  { key: 'fullAccess', label: 'Full Access' },
  { key: 'manageStaff', label: 'Manage Staff' },
  { key: 'financialControl', label: 'Financial Control' },
  { key: 'systemSettings', label: 'System Settings' },
  { key: 'userManagement', label: 'User Management' },
  { key: 'contentEditing', label: 'Content Editing' },
  { key: 'approveApps', label: 'Approve Applications' },
  { key: 'viewPayments', label: 'View Payments' },
  { key: 'exportReports', label: 'Export Reports' },
  { key: 'sendNotifications', label: 'Send Notifications' },
  { key: 'resetPasswords', label: 'Reset Passwords' }
]

// Watch filter changes
watch(filterRole, () => { currentPage.value = 1 })

const filteredStaff = computed(() => {
  let result = staff.value
  if (debouncedQuery.value) {
    const q = debouncedQuery.value.toLowerCase()
    result = result.filter(s => s.name.toLowerCase().includes(q) || s.email.toLowerCase().includes(q))
  }
  if (filterRole.value !== 'all') {
    result = result.filter(s => s.role === filterRole.value)
  }
  return result
})

const totalPages = computed(() => Math.max(1, Math.ceil(filteredStaff.value.length / pageSize.value)))

const paginatedStaff = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value
  const end = start + pageSize.value
  return filteredStaff.value.slice(start, end)
})

const paginationStart = computed(() => {
  if (filteredStaff.value.length === 0) return 0
  return (currentPage.value - 1) * pageSize.value + 1
})

const paginationEnd = computed(() => {
  return Math.min(currentPage.value * pageSize.value, filteredStaff.value.length)
})

const visiblePages = computed(() => {
  const pages: number[] = []
  const total = totalPages.value
  const current = currentPage.value
  if (total <= 7) {
    for (let i = 1; i <= total; i++) pages.push(i)
  } else {
    if (current <= 4) {
      for (let i = 1; i <= 5; i++) pages.push(i)
      pages.push(-1)
      pages.push(total)
    } else if (current >= total - 3) {
      pages.push(1)
      pages.push(-1)
      for (let i = total - 4; i <= total; i++) pages.push(i)
    } else {
      pages.push(1)
      pages.push(-1)
      for (let i = current - 1; i <= current + 1; i++) pages.push(i)
      pages.push(-1)
      pages.push(total)
    }
  }
  return pages
})

const viewStaff = (id: number) => {
  selectedStaff.value = staff.value.find(s => s.id === id)
  showPanel.value = true
}

const editStaff = (id: number) => {
  closePanel()
  const member = staff.value.find(s => s.id === id)
  if (member) {
    editForm.value = {
      name: member.name,
      email: member.email,
      role: member.role,
      password: '',
      status: member.status || 'active',
      permissions: [...(member.permissions || [])]
    }
    editModal.value = true
  }
}

const openAddModal = () => {
  addForm.value = { name: '', email: '', role: 'staff', password: '', permissions: [] }
  addModal.value = true
}

const closeAddModal = () => { addModal.value = false }

const addStaffMember = () => {
  if (!addForm.value.name || !addForm.value.email || !addForm.value.password) {
    alert('Please fill in all required fields')
    return
  }
  const newId = Math.max(...staff.value.map(s => s.id)) + 1
  const newUserCode = `2026${String(newId).padStart(5, '0')}`
  staff.value.push({
    id: newId,
    userCode: newUserCode,
    name: addForm.value.name,
    email: addForm.value.email,
    role: addForm.value.role,
    permissions: addForm.value.role === 'master' ? ['editUsers', 'suspendUsers', 'resetPasswords', 'approveAccounts', 'viewCommission', 'updateCommission', 'exportData', 'manageStaff', 'deleteStaff'] : addForm.value.permissions,
    registeredAt: new Date().toISOString().split('T')[0],
    lastLogin: '—',
    status: 'active'
  })
  alert(`${addForm.value.name} has been added as ${formatRole(addForm.value.role)}`)
  addModal.value = false
}

const saveEdit = () => {
  alert('Staff member updated')
  editModal.value = false
}

const closeEditModal = () => { editModal.value = false }

const suspendUser = () => {
  if (confirm(`Suspend account for ${editForm.value.name}? The user will not be able to log in until reactivated.`)) {
    editForm.value.status = 'suspended'
    alert(`Account suspended for ${editForm.value.name}`)
  }
}

const unsuspendUser = () => {
  if (confirm(`Reactivate account for ${editForm.value.name}? The user will be able to log in again.`)) {
    editForm.value.status = 'active'
    alert(`Account reactivated for ${editForm.value.name}`)
  }
}

const openDeleteConfirm = () => {
  deleteForm.value = { reason: '', file: null }
  editModal.value = false
  deleteModal.value = true
}

const closeDeleteModal = () => { deleteModal.value = false }

const triggerFileUpload = () => { deleteFileInput.value?.click() }

const handleDeleteFileChange = (e: Event) => {
  const target = e.target as HTMLInputElement
  if (target.files && target.files[0]) {
    const file = target.files[0]
    if (file.size > 5 * 1024 * 1024) {
      alert('File size must be less than 5MB')
      return
    }
    deleteForm.value.file = file
  }
}

const removeDeleteFile = () => {
  deleteForm.value.file = null
  if (deleteFileInput.value) deleteFileInput.value.value = ''
}

const confirmDeleteStaff = () => {
  if (!deleteForm.value.reason.trim()) {
    alert('Please enter a reason for deletion')
    return
  }
  if (confirm(`⚠️ Confirm deletion of ${editForm.value.name}? This action cannot be undone.`)) {
    const staffToDelete = staff.value.find(s => s.email === editForm.value.email)
    if (staffToDelete) {
      deletedStaff.value.unshift({
        userCode: staffToDelete.userCode,
        name: staffToDelete.name,
        email: staffToDelete.email,
        deletedAt: new Date().toLocaleString('en-GB', { timeZone: 'Asia/Hong_Kong' }),
        deletedBy: 'Admin',
        reason: deleteForm.value.reason,
        evidenceFile: deleteForm.value.file?.name || null
      })
      staff.value = staff.value.filter(s => s.id !== staffToDelete.id)
    }
    alert(`Account deleted for ${editForm.value.name}`)
    deleteModal.value = false
    closePanel()
  }
}

const toggleMenu = (id: number) => { alert(`More options for staff ${id} — coming soon`) }

const closePanel = () => {
  showPanel.value = false
  selectedStaff.value = null
}

const exportStaff = () => { alert('Export CSV — coming soon') }

// Role Permissions Management
const roleEditMode = ref(false)
const rolesBackup = ref<any[]>([])
const addRoleModal = ref(false)
const addRoleForm = ref({ name: '', icon: '👤', permissions: [] as string[] })
const editRoleModal = ref(false)
const editRoleForm = ref({ key: '', name: '', icon: '', permissions: [] as string[] })

const toggleRolePermission = (roleKey: string, permLabel: string) => {
  const role = roles.value.find(r => r.key === roleKey)
  if (role) {
    const idx = role.permissions.indexOf(permLabel)
    if (idx > -1) {
      role.permissions.splice(idx, 1)
    } else {
      role.permissions.push(permLabel)
    }
  }
}

const cancelRoleEdits = () => {
  roles.value = JSON.parse(JSON.stringify(rolesBackup.value))
  roleEditMode.value = false
}

const saveRoleEdits = () => {
  rolesBackup.value = JSON.parse(JSON.stringify(roles.value))
  roleEditMode.value = false
  alert('Role permissions saved successfully!')
}

const openAddRoleModal = () => {
  addRoleForm.value = { name: '', icon: '👤', permissions: [] }
  addRoleModal.value = true
}

const closeAddRoleModal = () => { addRoleModal.value = false }

const confirmAddRole = () => {
  if (!addRoleForm.value.name.trim()) {
    alert('Please enter a role name')
    return
  }
  const newKey = addRoleForm.value.name.toLowerCase().replace(/\s+/g, '_')
  if (roles.value.some(r => r.key === newKey)) {
    alert('A role with this name already exists')
    return
  }
  roles.value.push({
    key: newKey,
    name: addRoleForm.value.name,
    icon: addRoleForm.value.icon || '👤',
    permissions: [...addRoleForm.value.permissions]
  })
  rolesBackup.value = JSON.parse(JSON.stringify(roles.value))
  alert(`Role "${addRoleForm.value.name}" added successfully!`)
  addRoleModal.value = false
}

const openEditRoleModal = (role: any) => {
  editRoleForm.value = {
    key: role.key,
    name: role.name,
    icon: role.icon,
    permissions: [...role.permissions]
  }
  editRoleModal.value = true
}

const closeEditRoleModal = () => { editRoleModal.value = false }

const allRolePermsTicked = computed(() => {
  return allPermissionLabels.every(p => editRoleForm.value.permissions.includes(p.label))
})

const toggleAllRolePermissions = () => {
  if (allRolePermsTicked.value) {
    editRoleForm.value.permissions = []
  } else {
    editRoleForm.value.permissions = allPermissionLabels.map(p => p.label)
  }
}

const confirmEditRole = () => {
  if (!editRoleForm.value.name.trim()) {
    alert('Please enter a role name')
    return
  }
  const role = roles.value.find(r => r.key === editRoleForm.value.key)
  if (role) {
    role.name = editRoleForm.value.name
    role.icon = editRoleForm.value.icon || '👤'
    role.permissions = [...editRoleForm.value.permissions]
    rolesBackup.value = JSON.parse(JSON.stringify(roles.value))
    alert(`Role "${role.name}" updated successfully!`)
  }
  editRoleModal.value = false
}

const confirmDeleteRole = () => {
  if (!confirm(`⚠️ Delete the role "${editRoleForm.value.name}"? Existing staff with this role will not be affected.`)) return
  roles.value = roles.value.filter(r => r.key !== editRoleForm.value.key)
  rolesBackup.value = JSON.parse(JSON.stringify(roles.value))
  alert(`Role "${editRoleForm.value.name}" deleted.`)
  editRoleModal.value = false
}

// Initialize roles backup
rolesBackup.value = JSON.parse(JSON.stringify(roles.value))
</script>

<style scoped>
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

* { margin: 0; padding: 0; box-sizing: border-box; }

.bsp-dashboard {
  min-height: 100vh;
  background: var(--bsp-light);
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: var(--bsp-dark);
}

.dashboard-body { display: flex; min-height: 100vh; }

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

.nav-section { margin-bottom: 1.5rem; }

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

.nav-item:hover { background: #f1f5f9; color: var(--bsp-primary); }
.nav-item.active { background: #eff6ff; color: var(--bsp-primary); border-left-color: var(--bsp-secondary); font-weight: 600; }
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
.main-content { flex: 1; padding: 1.5rem 2rem; overflow-y: auto; min-width: 0; }

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1.5rem;
}

.page-title { font-size: 1.5rem; font-weight: 700; color: var(--bsp-primary); margin-bottom: 0.25rem; }
.page-subtitle { font-size: 0.9rem; color: #64748b; }

.btn {
  padding: 0.6rem 1.2rem;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  border: none;
  transition: all 0.2s;
}

.btn-primary { background: var(--bsp-primary); color: white; }
.btn-primary:hover { background: #1a2644; }
.btn-secondary { background: white; color: var(--bsp-primary); border: 1px solid #e2e8f0; }
.btn-secondary:hover { background: #f8fafc; }
.btn-danger { background: var(--bsp-danger); color: white; }
.btn-danger:hover { background: #dc2626; }
.btn-success { background: var(--bsp-success); color: white; }
.btn-success:hover { background: #059669; }

.page-actions { display: flex; gap: 0.75rem; }

/* Tabs */
.tab-bar { display: flex; gap: 0.5rem; margin-bottom: 1.5rem; border-bottom: 1px solid #e2e8f0; padding-bottom: 0; }

.tab-btn {
  padding: 0.75rem 1.25rem;
  background: none;
  border: none;
  border-bottom: 2px solid transparent;
  font-size: 0.9rem;
  font-weight: 500;
  color: #64748b;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.tab-btn:hover { color: var(--bsp-primary); }
.tab-btn.active { color: var(--bsp-primary); border-bottom-color: var(--bsp-secondary); font-weight: 600; }

.tab-count {
  background: #f1f5f9;
  color: #64748b;
  font-size: 0.75rem;
  font-weight: 600;
  padding: 0.15rem 0.5rem;
  border-radius: 4px;
}

.tab-count.deleted { background: #fee2e2; color: #991b1b; }

/* Filter Bar */
.filter-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  gap: 1rem;
}

.filter-left { display: flex; gap: 0.75rem; flex: 1; }

.search-box {
  display: flex;
  align-items: center;
  background: white;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 0 0.75rem;
  flex: 1;
  max-width: 300px;
  overflow: hidden;
}

.search-clear {
  background: none;
  border: none;
  font-size: 1.1rem;
  color: #94a3b8;
  cursor: pointer;
  padding: 0 0.25rem;
  margin-left: 0.25rem;
  line-height: 1;
}

.search-clear:hover { color: #64748b; }
.search-icon { font-size: 1rem; margin-right: 0.5rem; }

.search-input {
  border: none;
  outline: none;
  padding: 0.6rem 0;
  font-size: 0.9rem;
  width: 100%;
  background: transparent;
}

.filter-select {
  padding: 0.6rem 0.75rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.9rem;
  background: white;
  color: var(--bsp-dark);
  cursor: pointer;
}

.result-count { font-size: 0.85rem; color: #64748b; font-weight: 500; }

/* Users Table */
.users-table-wrapper {
  background: white;
  border-radius: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  overflow-x: auto;
  margin-bottom: 1rem;
}

.users-table { width: 100%; min-width: 700px; border-collapse: collapse; }
.users-table thead { background: #f8fafc; }

.users-table th {
  text-align: left;
  padding: 0.875rem 1rem;
  font-size: 0.75rem;
  font-weight: 600;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  border-bottom: 1px solid #e2e8f0;
}

.th-role, .th-permissions, .th-login, .th-status, .th-actions { text-align: center !important; }

.user-row:hover { background: #fafbfc; }

.users-table td {
  padding: 1rem;
  border-bottom: 1px solid #f1f5f9;
  vertical-align: middle;
}

.td-user { text-align: left !important; }
.td-role, .td-permissions, .td-login, .td-status, .td-actions { text-align: center !important; }

.user-info-cell { display: flex; align-items: center; gap: 0.75rem; }

.user-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--bsp-primary);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 0.85rem;
  flex-shrink: 0;
}

.deleted-avatar { background: #94a3b8; }

.user-name { display: block; font-weight: 600; color: var(--bsp-dark); font-size: 0.9rem; }
.user-email { display: block; font-size: 0.8rem; color: #64748b; }

.user-code-inline {
  font-family: monospace;
  font-size: 0.75rem;
  color: #94a3b8;
  background: #f1f5f9;
  padding: 0.1rem 0.4rem;
  border-radius: 4px;
  display: inline-block;
  margin-top: 0.2rem;
}

.deleted-code { background: #fee2e2; color: #991b1b; }

/* Role Badges */
.role-badge {
  padding: 0.25rem 0.6rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}

.role-master { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
.role-manager { background: #eff6ff; color: #1d4ed8; }
.role-staff { background: #f1f5f9; color: #475569; }

/* Permissions */
.permission-badges { display: flex; flex-wrap: wrap; gap: 0.25rem; justify-content: center; }

.perm-badge {
  background: #f1f5f9;
  color: #475569;
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
  font-size: 0.7rem;
  font-weight: 500;
}

.perm-more {
  background: #e2e8f0;
  color: #64748b;
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
  font-size: 0.7rem;
  font-weight: 500;
}

/* Login */
.login-text { font-size: 0.85rem; color: #64748b; font-family: monospace; }

/* Status Badges */
.status-badge {
  padding: 0.25rem 0.6rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}

.status-active { background: #d1fae5; color: #047857; }
.status-suspended { background: #fef3c7; color: #92400e; }
.status-inactive { background: #f1f5f9; color: #64748b; }

/* Action Buttons */
.action-buttons { display: flex; justify-content: center; gap: 0.35rem; }

.btn-action {
  background: none;
  border: none;
  font-size: 1rem;
  cursor: pointer;
  padding: 0.35rem;
  border-radius: 4px;
  transition: background 0.2s;
}

.btn-action:hover { background: #f1f5f9; }

/* Pagination */
.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 1rem;
  flex-wrap: wrap;
  gap: 0.75rem;
}

.pagination-info { font-size: 0.85rem; color: #64748b; }

.pagination-controls { display: flex; gap: 0.35rem; }

.btn-page {
  padding: 0.4rem 0.75rem;
  border: 1px solid #e2e8f0;
  background: white;
  border-radius: 6px;
  font-size: 0.85rem;
  cursor: pointer;
  color: #64748b;
  transition: all 0.2s;
}

.btn-page:hover:not(:disabled) { background: #f8fafc; color: var(--bsp-primary); }
.btn-page.active { background: var(--bsp-primary); color: white; border-color: var(--bsp-primary); }
.btn-page:disabled { opacity: 0.5; cursor: not-allowed; }

/* Empty State */
.empty-state { text-align: center; padding: 3rem 1rem; }
.empty-content { display: flex; flex-direction: column; align-items: center; gap: 0.5rem; }
.empty-icon { font-size: 2.5rem; }
.empty-content p { color: #94a3b8; font-size: 0.9rem; }

/* Settings Section (for Role Permissions tab) */
.settings-section {
  background: white;
  border-radius: 10px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}

.section-header { margin-bottom: 1.25rem; }
.section-title { font-size: 1.1rem; font-weight: 600; color: #1e293b; }
.section-desc { font-size: 0.85rem; color: #64748b; margin-top: 0.25rem; }

/* Permissions Grid */
.permissions-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 1rem; }

.permission-card {
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 1rem;
  display: flex;
  flex-direction: column;
}

.permission-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.75rem;
}

.permission-title-row { display: flex; align-items: center; gap: 0.5rem; }
.role-icon { font-size: 1.25rem; }
.permission-title { font-size: 0.95rem; font-weight: 600; }
.permission-count { font-size: 0.75rem; color: #94a3b8; }

.permission-list { list-style: none; padding: 0; margin: 0; flex: 1; }
.permission-list li { padding: 0.35rem 0; font-size: 0.85rem; color: #475569; border-bottom: 1px solid #f1f5f9; }
.permission-list li:last-child { border-bottom: none; }

.permission-footer { margin-top: 0.75rem; padding-top: 0.75rem; border-top: 1px solid #e2e8f0; }
.staff-count { font-size: 0.8rem; color: #94a3b8; }

/* Matrix Table */
.matrix-table-wrapper { overflow-x: auto; }
.matrix-table { width: 100%; border-collapse: collapse; font-size: 0.85rem; }
.matrix-table th {
  text-align: center;
  padding: 0.75rem;
  font-size: 0.75rem;
  font-weight: 700;
  color: #64748b;
  text-transform: uppercase;
  border-bottom: 1px solid #e2e8f0;
  background: #f8fafc;
}
.matrix-table td { padding: 0.75rem; border-bottom: 1px solid #f1f5f9; text-align: center; }
.matrix-perm-name { text-align: left !important; font-weight: 500; }
.check-yes { color: var(--bsp-success); font-weight: 700; font-size: 1rem; }
.check-no { color: #cbd5e1; }

/* Role Permissions */
.section-header-row {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1.25rem;
  gap: 1rem;
}

.section-actions { display: flex; gap: 0.5rem; align-items: center; }

.btn-edit-role {
  background: none;
  border: none;
  font-size: 0.85rem;
  cursor: pointer;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  opacity: 0.6;
  transition: all 0.2s;
}

.btn-edit-role:hover { opacity: 1; background: #e2e8f0; }

/* Matrix Checkbox */
.matrix-checkbox {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  position: relative;
}

.matrix-checkbox input[type="checkbox"] {
  position: absolute;
  opacity: 0;
  width: 100%;
  height: 100%;
  cursor: pointer;
}

.checkbox-custom {
  width: 22px;
  height: 22px;
  border: 2px solid #e2e8f0;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  font-weight: 700;
  transition: all 0.2s;
  color: transparent;
}

.checkbox-custom.checked {
  background: #10b981;
  border-color: #10b981;
  color: white;
}

.matrix-checkbox:hover .checkbox-custom {
  border-color: #10b981;
}

.role-icon-small { font-size: 0.9rem; margin-right: 0.25rem; }

.matrix-role-header { text-align: center; }

.small-warning { padding: 0.75rem; }

/* Detail Panel */
.detail-panel-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.45);
  z-index: 1000;
  display: flex;
  justify-content: flex-end;
}

.detail-panel {
  width: 100%;
  max-width: 420px;
  background: white;
  height: 100vh;
  overflow-y: auto;
  box-shadow: -10px 0 40px rgba(0,0,0,0.15);
  animation: slideIn 0.3s ease;
}

@keyframes slideIn {
  from { transform: translateX(100%); }
  to { transform: translateX(0); }
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.25rem;
  border-bottom: 1px solid #e2e8f0;
}

.panel-title { font-size: 1.1rem; font-weight: 700; }
.panel-close { background: none; border: none; font-size: 1.5rem; color: #94a3b8; cursor: pointer; }
.panel-close:hover { color: var(--bsp-dark); }

.panel-body { padding: 1.25rem; }
.panel-section { margin-bottom: 1.5rem; }
.panel-section .section-title { font-size: 0.85rem; font-weight: 700; color: #94a3b8; text-transform: uppercase; margin-bottom: 0.75rem; }

.info-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 0.75rem; }
.info-item { display: flex; flex-direction: column; gap: 0.25rem; }
.info-item.full-width { grid-column: 1 / -1; }
.info-label { font-size: 0.75rem; color: #94a3b8; font-weight: 500; }
.info-value { font-size: 0.9rem; color: var(--bsp-dark); font-weight: 500; }

.panel-permissions-list { display: flex; flex-wrap: wrap; gap: 0.35rem; }
.panel-perm-badge {
  background: #f1f5f9;
  color: #475569;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-size: 0.75rem;
}
.no-permissions { color: #94a3b8; font-size: 0.85rem; }

.panel-footer {
  display: flex;
  justify-content: flex-end;
  gap: 0.75rem;
  padding: 1rem 1.25rem;
  border-top: 1px solid #e2e8f0;
}

/* Modal */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.45);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 1rem;
}

.modal-box {
  background: white;
  border-radius: 12px;
  width: 100%;
  max-width: 520px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 20px 60px rgba(0,0,0,0.2);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.modal-title { font-size: 1.1rem; font-weight: 700; }
.modal-close { background: none; border: none; font-size: 1.5rem; color: #94a3b8; cursor: pointer; }
.modal-close:hover { color: var(--bsp-dark); }

.modal-body { padding: 1.5rem; display: flex; flex-direction: column; gap: 1rem; }
.modal-footer { display: flex; justify-content: flex-end; gap: 0.75rem; padding: 1rem 1.5rem; border-top: 1px solid #e2e8f0; background: #f8fafc; }

/* Form */
.form-group { display: flex; flex-direction: column; gap: 0.4rem; }
.form-label { font-size: 0.8rem; font-weight: 600; color: #64748b; }
.form-input {
  padding: 0.65rem 0.875rem;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  font-size: 0.9rem;
  font-family: inherit;
}
.form-input:focus { outline: none; border-color: var(--bsp-secondary); box-shadow: 0 0 0 3px rgba(59,130,246,0.1); }
.label-hint { font-weight: 400; color: #94a3b8; }
.required { color: var(--bsp-danger); }

/* Permissions in Modal */
.modal-divider { height: 1px; background: #e2e8f0; margin: 0.5rem 0; }
.permissions-section { display: flex; flex-direction: column; gap: 0.75rem; }
.permissions-header { display: flex; justify-content: space-between; align-items: center; }
.permissions-title { font-size: 0.9rem; font-weight: 600; }
.btn-link { background: none; border: none; color: var(--bsp-secondary); font-size: 0.8rem; cursor: pointer; }
.btn-link:hover { text-decoration: underline; }

.permissions-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 0.5rem; }

.permission-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #e2e8f0;
  cursor: pointer;
  transition: all 0.2s;
}

.permission-item:hover { background: #f8fafc; }
.permission-item.permission-danger { border-color: #fecaca; }
.permission-item.permission-danger:hover { background: #fef2f2; }

.permission-label { font-size: 0.85rem; font-weight: 500; }
.permission-desc { font-size: 0.75rem; color: #94a3b8; display: block; margin-top: 0.15rem; }
.permissions-note { font-size: 0.8rem; color: #94a3b8; margin-top: 0.5rem; }

/* Status Display */
.status-display { display: flex; align-items: center; gap: 0.5rem; }

/* Action Buttons in Modal */
.btn-action-text {
  display: flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  font-size: 0.85rem;
  font-weight: 500;
  cursor: pointer;
  border: 1px solid #e2e8f0;
  background: white;
  color: #64748b;
}

.btn-action-text:hover { background: #f8fafc; }
.btn-action-text.btn-danger { color: var(--bsp-danger); border-color: #fecaca; }
.btn-action-text.btn-danger:hover { background: #fef2f2; }
.btn-action-text.btn-success { color: var(--bsp-success); border-color: #a7f3d0; }
.btn-action-text.btn-success:hover { background: #ecfdf5; }

/* Delete Warning */
.delete-warning {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem;
  background: #fef2f2;
  border: 1px solid #fecaca;
  border-radius: 8px;
  margin-bottom: 0.5rem;
}

.warning-icon { font-size: 1.25rem; }
.delete-warning p { font-size: 0.9rem; color: #7f1d1d; margin: 0; }
.delete-warning strong { font-weight: 600; }

/* File Upload */
.form-textarea {
  padding: 0.65rem 0.875rem;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  font-size: 0.9rem;
  font-family: inherit;
  resize: vertical;
  min-height: 80px;
}

.file-upload-area {
  border: 2px dashed #e2e8f0;
  border-radius: 8px;
  padding: 1rem;
  text-align: center;
  cursor: pointer;
  transition: all 0.2s;
}

.file-upload-area:hover { border-color: #cbd5e1; background: #f8fafc; }

.upload-placeholder { display: flex; flex-direction: column; align-items: center; gap: 0.35rem; color: #94a3b8; font-size: 0.85rem; }
.upload-icon { font-size: 1.5rem; }
.upload-hint { font-size: 0.75rem; }

.file-selected {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  color: var(--bsp-dark);
  font-size: 0.9rem;
}

.file-icon { font-size: 1.25rem; }
.file-name { font-weight: 500; }
.btn-remove-file {
  background: none;
  border: none;
  font-size: 1.25rem;
  color: #94a3b8;
  cursor: pointer;
  padding: 0;
  line-height: 1;
}

.btn-remove-file:hover { color: var(--bsp-danger); }
.form-hint { font-size: 0.75rem; color: #94a3b8; margin-top: 0.15rem; }
.warning-hint { color: var(--bsp-warning); }

/* Responsive */
@media (max-width: 1024px) {
  .permissions-grid { grid-template-columns: repeat(2, 1fr); }
  .matrix-table { font-size: 0.8rem; }
}

@media (max-width: 768px) {
  .sidebar { display: none; }
  .main-content { padding: 1rem; }
  .permissions-grid { grid-template-columns: 1fr; }
  .page-header { flex-direction: column; gap: 1rem; align-items: flex-start; }
  .filter-left { flex-direction: column; width: 100%; }
  .search-box { max-width: 100%; }
  .tab-bar { overflow-x: auto; }
  .tab-btn { white-space: nowrap; }
}
</style>