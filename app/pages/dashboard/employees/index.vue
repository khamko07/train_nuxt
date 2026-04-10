<template>
  <div>
    <div class="d-flex align-center justify-space-between mb-6">
      <div>
        <div class="text-h5 font-weight-bold">Employee Management</div>
        <div class="text-body-2 text-medium-emphasis">Manage all company employees</div>
      </div>
      <v-btn color="blue" prepend-icon="mdi-plus" @click="openCreateDialog">
        Add Employee
      </v-btn>
    </div>

    <!-- Search & Filter -->
    <v-card rounded="lg" elevation="2" class="mb-4">
      <v-card-text>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="search"
              density="compact"
              placeholder="Search by name or email..."
              prepend-inner-icon="mdi-magnify"
              variant="outlined"
              hide-details
              clearable
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6" md="3">
            <v-select
              v-model="filterDepartment"
              :items="['All', ...departments]"
              density="compact"
              label="Department"
              variant="outlined"
              hide-details
            ></v-select>
          </v-col>
          <v-col cols="12" sm="6" md="3">
            <v-select
              v-model="filterStatus"
              :items="['All', 'Active', 'Inactive']"
              density="compact"
              label="Status"
              variant="outlined"
              hide-details
            ></v-select>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>

    <!-- Data Table -->
    <v-card rounded="lg" elevation="2">
      <v-data-table
        :headers="headers"
        :items="filteredEmployees"
        :search="search"
        items-per-page="10"
        class="elevation-0"
      >
        <template #item.name="{ item }">
          <div class="d-flex align-center">
            <v-avatar color="blue" size="32" class="mr-3">
              <span class="text-body-2 text-white font-weight-bold">{{ item.name.charAt(0) }}</span>
            </v-avatar>
            <div>
              <div class="text-body-2 font-weight-bold">{{ item.name }}</div>
              <div class="text-caption text-medium-emphasis">{{ item.email }}</div>
            </div>
          </div>
        </template>

        <template #item.status="{ item }">
          <v-chip
            :color="item.status === 'Active' ? 'green' : 'red'"
            size="small"
            variant="tonal"
          >
            <v-icon start :icon="item.status === 'Active' ? 'mdi-check-circle' : 'mdi-close-circle'"></v-icon>
            {{ item.status }}
          </v-chip>
        </template>

        <template #item.actions="{ item }">
          <v-btn icon variant="text" size="small" color="blue" @click="openEditDialog(item)">
            <v-icon>mdi-pencil</v-icon>
            <v-tooltip activator="parent">Edit</v-tooltip>
          </v-btn>
          <v-btn icon variant="text" size="small" color="red" @click="openDeleteDialog(item)">
            <v-icon>mdi-delete</v-icon>
            <v-tooltip activator="parent">Delete</v-tooltip>
          </v-btn>
        </template>
      </v-data-table>
    </v-card>

    <!-- Create / Edit Dialog -->
    <v-dialog v-model="formDialog" max-width="600" persistent>
      <v-card rounded="lg">
        <v-card-title class="d-flex align-center pa-4">
          <v-icon class="mr-2" color="blue">{{ isEditing ? 'mdi-pencil' : 'mdi-account-plus' }}</v-icon>
          {{ isEditing ? 'Edit Employee' : 'Add New Employee' }}
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text class="pa-4">
          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formData.name"
                label="Full Name *"
                prepend-inner-icon="mdi-account-outline"
                variant="outlined"
                density="compact"
                :error-messages="formErrors.name"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formData.email"
                label="Email *"
                prepend-inner-icon="mdi-email-outline"
                variant="outlined"
                density="compact"
                :error-messages="formErrors.email"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formData.phone"
                label="Phone"
                prepend-inner-icon="mdi-phone-outline"
                variant="outlined"
                density="compact"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-select
                v-model="formData.department"
                :items="departments"
                label="Department *"
                prepend-inner-icon="mdi-office-building-outline"
                variant="outlined"
                density="compact"
                :error-messages="formErrors.department"
              ></v-select>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formData.position"
                label="Position *"
                prepend-inner-icon="mdi-briefcase-outline"
                variant="outlined"
                density="compact"
                :error-messages="formErrors.position"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formData.joinDate"
                label="Join Date *"
                prepend-inner-icon="mdi-calendar-outline"
                variant="outlined"
                density="compact"
                type="date"
                :error-messages="formErrors.joinDate"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-select
                v-model="formData.status"
                :items="['Active', 'Inactive']"
                label="Status *"
                prepend-inner-icon="mdi-toggle-switch-outline"
                variant="outlined"
                density="compact"
              ></v-select>
            </v-col>
          </v-row>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions class="pa-4">
          <v-spacer></v-spacer>
          <v-btn variant="text" @click="closeFormDialog">Cancel</v-btn>
          <v-btn color="blue" variant="tonal" @click="saveEmployee">
            {{ isEditing ? 'Save Changes' : 'Add Employee' }}
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Delete Confirmation Dialog -->
    <v-dialog v-model="deleteDialog" max-width="400">
      <v-card rounded="lg">
        <v-card-title class="d-flex align-center pa-4">
          <v-icon class="mr-2" color="red">mdi-alert-circle-outline</v-icon>
          Confirm Delete
        </v-card-title>
        <v-card-text class="pa-4">
          Are you sure you want to delete employee
          <strong>{{ selectedEmployee?.name }}</strong>? This action cannot be undone.
        </v-card-text>
        <v-card-actions class="pa-4">
          <v-spacer></v-spacer>
          <v-btn variant="text" @click="deleteDialog = false">Cancel</v-btn>
          <v-btn color="red" variant="tonal" @click="confirmDelete">Delete</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Snackbar Notification -->
    <v-snackbar v-model="snackbar.show" :color="snackbar.color" timeout="3000" location="top right">
      <v-icon class="mr-2">{{ snackbar.icon }}</v-icon>
      {{ snackbar.message }}
    </v-snackbar>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

definePageMeta({ layout: 'dashboard' })

// Shared employee state (same as dashboard)
const employees = useState('employees', () => [
  { id: 1, name: 'Nguyen Van A', email: 'vana@company.com', department: 'Engineering', position: 'Frontend Developer', phone: '0901234567', status: 'Active', joinDate: '2023-01-15' },
  { id: 2, name: 'Tran Thi B', email: 'thib@company.com', department: 'Marketing', position: 'Marketing Manager', phone: '0902345678', status: 'Active', joinDate: '2022-06-01' },
  { id: 3, name: 'Le Van C', email: 'vanc@company.com', department: 'Engineering', position: 'Backend Developer', phone: '0903456789', status: 'Active', joinDate: '2023-03-10' },
  { id: 4, name: 'Pham Thi D', email: 'thid@company.com', department: 'HR', position: 'HR Specialist', phone: '0904567890', status: 'Inactive', joinDate: '2021-11-20' },
  { id: 5, name: 'Hoang Van E', email: 'vane@company.com', department: 'Engineering', position: 'DevOps Engineer', phone: '0905678901', status: 'Active', joinDate: '2023-07-05' },
  { id: 6, name: 'Vu Thi F', email: 'thif@company.com', department: 'Finance', position: 'Accountant', phone: '0906789012', status: 'Active', joinDate: '2022-09-12' },
])

const departments = ['Engineering', 'Marketing', 'HR', 'Finance', 'Sales', 'Operations']

// Table headers
const headers = [
  { title: 'Employee', key: 'name', sortable: true },
  { title: 'Department', key: 'department', sortable: true },
  { title: 'Position', key: 'position', sortable: true },
  { title: 'Phone', key: 'phone', sortable: false },
  { title: 'Join Date', key: 'joinDate', sortable: true },
  { title: 'Status', key: 'status', sortable: true },
  { title: 'Actions', key: 'actions', sortable: false, align: 'center' },
]

// Filter state
const search = ref('')
const filterDepartment = ref('All')
const filterStatus = ref('All')

const filteredEmployees = computed(() => {
  return employees.value.filter(emp => {
    const matchDept = filterDepartment.value === 'All' || emp.department === filterDepartment.value
    const matchStatus = filterStatus.value === 'All' || emp.status === filterStatus.value
    return matchDept && matchStatus
  })
})

// Form dialog
const formDialog = ref(false)
const isEditing = ref(false)
const selectedEmployee = ref(null)

const emptyForm = () => ({
  name: '',
  email: '',
  phone: '',
  department: '',
  position: '',
  joinDate: '',
  status: 'Active',
})

const formData = ref(emptyForm())
const formErrors = ref({ name: '', email: '', department: '', position: '', joinDate: '' })

function validateForm() {
  let valid = true
  formErrors.value = { name: '', email: '', department: '', position: '', joinDate: '' }
  if (!formData.value.name.trim()) { formErrors.value.name = 'Name is required'; valid = false }
  if (!formData.value.email.trim()) {
    formErrors.value.email = 'Email is required'; valid = false
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.value.email)) {
    formErrors.value.email = 'Invalid email address'; valid = false
  }
  if (!formData.value.department) { formErrors.value.department = 'Department is required'; valid = false }
  if (!formData.value.position.trim()) { formErrors.value.position = 'Position is required'; valid = false }
  if (!formData.value.joinDate) { formErrors.value.joinDate = 'Join date is required'; valid = false }
  return valid
}

function openCreateDialog() {
  isEditing.value = false
  formData.value = emptyForm()
  formErrors.value = { name: '', email: '', department: '', position: '', joinDate: '' }
  formDialog.value = true
}

function openEditDialog(employee) {
  isEditing.value = true
  selectedEmployee.value = employee
  formData.value = { ...employee }
  formErrors.value = { name: '', email: '', department: '', position: '', joinDate: '' }
  formDialog.value = true
}

function closeFormDialog() {
  formDialog.value = false
  selectedEmployee.value = null
}

function saveEmployee() {
  if (!validateForm()) return

  if (isEditing.value) {
    const idx = employees.value.findIndex(e => e.id === selectedEmployee.value.id)
    if (idx !== -1) {
      employees.value[idx] = { ...formData.value, id: selectedEmployee.value.id }
    }
    showSnackbar('Employee updated successfully', 'green', 'mdi-check-circle')
  } else {
    const newId = employees.value.length > 0 ? Math.max(...employees.value.map(e => e.id)) + 1 : 1
    employees.value.push({ ...formData.value, id: newId })
    showSnackbar('Employee added successfully', 'green', 'mdi-check-circle')
  }

  closeFormDialog()
}

// Delete dialog
const deleteDialog = ref(false)

function openDeleteDialog(employee) {
  selectedEmployee.value = employee
  deleteDialog.value = true
}

function confirmDelete() {
  employees.value = employees.value.filter(e => e.id !== selectedEmployee.value.id)
  showSnackbar('Employee deleted successfully', 'red', 'mdi-delete')
  deleteDialog.value = false
  selectedEmployee.value = null
}

// Snackbar
const snackbar = ref({ show: false, message: '', color: 'green', icon: 'mdi-check-circle' })

function showSnackbar(message, color, icon) {
  snackbar.value = { show: true, message, color, icon }
}
</script>
