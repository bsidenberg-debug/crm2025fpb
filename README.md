npx nuxi init buildertrend-crm
cd buildertrend-crm
npm install
npm install vuetify@^3.0.0 sass sass-loader@^12.0.0 -D
// plugins/vuetify.ts
import { createVuetify } from 'vuetify'
import * as components from 'vuetify/components'
import * as directives from 'vuetify/directives'
import 'vuetify/styles'

export default defineNuxtPlugin(nuxtApp => {
  const vuetify = createVuetify({
    components,
    directives,
  })

  nuxtApp.vueApp.use(vuetify)
})
<template>
  <v-app>
    <NuxtPage />
  </v-app>
</template>
buildertrend-crm/
├── components/        # Reusable UI components
├── layouts/           # Layouts (dashboard, auth)
├── pages/             # Nuxt auto-routed views
│   ├── index.vue      # Login Page
│   ├── register.vue   # Register Page
│   └── dashboard/     # Protected Pages
├── plugins/           # Vuetify, Auth, etc.
├── composables/       # useAuth, useProjectStore, etc.
└── stores/            # Pinia Stores
npm install @pinia/nuxt
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
})
// stores/auth.ts
import { defineStore } from 'pinia'

export const useAuthStore = defineStore('auth', {
  state: () => ({
    user: null as null | { email: string },
    token: null as null | string,
  }),
  actions: {
    login(email: string, password: string) {
      // Dummy login logic
      if (email === 'admin@demo.com' && password === 'password') {
        this.user = { email }
        this.token = 'dummy-token'
        return true
      }
      return false
    },
    register(email: string, password: string) {
      // Just set user directly for now
      this.user = { email }
      this.token = 'dummy-token'
    },
    logout() {
      this.user = null
      this.token = null
    },
    isAuthenticated() {
      return !!this.token
    }
  },
})
<template>
  <v-container>
    <v-card class="mx-auto" max-width="400">
      <v-card-title>Login</v-card-title>
      <v-card-text>
        <v-form @submit.prevent="handleLogin">
          <v-text-field v-model="email" label="Email" />
          <v-text-field v-model="password" label="Password" type="password" />
          <v-btn type="submit" color="primary">Login</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const auth = useAuthStore()
const email = ref('')
const password = ref('')

function handleLogin() {
  const success = auth.login(email.value, password.value)
  if (success) router.push('/dashboard')
  else alert('Invalid credentials')
}
</script>
<template>
  <v-container>
    <v-card class="mx-auto" max-width="400">
      <v-card-title>Register</v-card-title>
      <v-card-text>
        <v-form @submit.prevent="handleRegister">
          <v-text-field v-model="email" label="Email" />
          <v-text-field v-model="password" label="Password" type="password" />
          <v-btn type="submit" color="primary">Register</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const auth = useAuthStore()
const email = ref('')
const password = ref('')

function handleRegister() {
  auth.register(email.value, password.value)
  router.push('/dashboard')
}
</script>
<template>
  <v-container>
    <v-card class="mx-auto" max-width="400">
      <v-card-title>Register</v-card-title>
      <v-card-text>
        <v-form @submit.prevent="handleRegister">
          <v-text-field v-model="email" label="Email" />
          <v-text-field v-model="password" label="Password" type="password" />
          <v-btn type="submit" color="primary">Register</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const auth = useAuthStore()
const email = ref('')
const password = ref('')

function handleRegister() {
  auth.register(email.value, password.value)
  router.push('/dashboard')
}
</script>
// middleware/auth.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const auth = useAuthStore()

  // Allow login and register routes
  if (['/register', '/'].includes(to.path)) return

  // Block access to protected pages
  if (!auth.isAuthenticated()) {
    return navigateTo('/')
  }
})
<template>
  <v-app>
    <v-navigation-drawer app v-model="drawer" permanent>
      <v-list>
        <v-list-item title="Buildertrend CRM" subtitle="Dashboard" />
        <v-divider />

        <v-list-item
          v-for="link in links"
          :key="link.text"
          :to="link.to"
          :title="link.text"
          link
        />
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app>
      <v-app-bar-title>Dashboard</v-app-bar-title>
      <v-spacer />
      <v-btn icon @click="logout">
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <NuxtPage />
    </v-main>
  </v-app>
</template>

<script setup>
import { useAuthStore } from '@/stores/auth'

const drawer = ref(true)
const auth = useAuthStore()

const logout = () => {
  auth.logout()
  navigateTo('/')
}

const links = [
  { text: 'Home', to: '/dashboard' },
  { text: 'Projects', to: '/dashboard/projects' },
  { text: 'Schedule', to: '/dashboard/schedule' },
  { text: 'Clients', to: '/dashboard/clients' },
]
</script>
<template>
  <v-container>
    <h1>Welcome to Buildertrend CRM</h1>
    <p>This is your dashboard.</p>
  </v-container>
</template>

<script setup>
definePageMeta({ layout: 'dashboard' })
</script>
const links = [
  { text: 'Home', to: '/dashboard' },
  { text: 'Projects', to: '/dashboard/projects' },
  { text: 'Schedule', to: '/dashboard/schedule' },
  { text: 'Clients', to: '/dashboard/clients' },
]
export default defineNuxtRouteMiddleware((to, from) => {
  const auth = useAuthStore()

  // Allow unauthenticated access only to login and register
  if (['/', '/register'].includes(to.path)) return

  // Redirect unauthenticated users
  if (!auth.isAuthenticated()) {
    return navigateTo('/')
  }
})
export default defineNuxtRouteMiddleware(() => {
  const auth = useAuthStore()
  if (!auth.isAuthenticated()) {
    return navigateTo('/')
  }
})
definePageMeta({
  middleware: 'auth'
})
<script setup>
definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})
</script>
<script setup>
definePageMeta({
  middleware: () => {
    const auth = useAuthStore()
    if (auth.isAuthenticated()) {
      return navigateTo('/dashboard')
    }
  }
})
</script>
// stores/projects.ts
import { defineStore } from 'pinia'

export const useProjectStore = defineStore('projects', {
  state: () => ({
    projects: [] as Array<{ id: number, name: string, client: string, status: string }>
  }),
  actions: {
    addProject(project: { name: string, client: string, status: string }) {
      const id = Date.now()
      this.projects.push({ id, ...project })
    },
    updateProject(id: number, updated: { name: string, client: string, status: string }) {
      const index = this.projects.findIndex(p => p.id === id)
      if (index !== -1) this.projects[index] = { id, ...updated }
    },
    deleteProject(id: number) {
      this.projects = this.projects.filter(p => p.id !== id)
    },
    getProject(id: number) {
      return this.projects.find(p => p.id === id)
    }
  }
})
<template>
  <v-container>
    <v-btn color="primary" @click="dialog = true">Add Project</v-btn>

    <v-data-table
      :items="projectStore.projects"
      :headers="headers"
      class="mt-4"
    >
      <template #item.actions="{ item }">
        <v-btn icon @click="edit(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="projectStore.deleteProject(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>{{ form.id ? 'Edit' : 'Add' }} Project</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="save">
            <v-text-field v-model="form.name" label="Project Name" />
            <v-text-field v-model="form.client" label="Client Name" />
            <v-select
              v-model="form.status"
              :items="['Planning', 'In Progress', 'Completed']"
              label="Status"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn @click="save" color="primary">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { useProjectStore } from '@/stores/projects'

definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const projectStore = useProjectStore()

const dialog = ref(false)
const form = ref({ id: null, name: '', client: '', status: '' })

const headers = [
  { title: 'Project Name', key: 'name' },
  { title: 'Client', key: 'client' },
  { title: 'Status', key: 'status' },
  { title: 'Actions', key: 'actions', sortable: false }
]

function edit(project) {
  form.value = { ...project }
  dialog.value = true
}

function save() {
  if (form.value.id) {
    projectStore.updateProject(form.value.id, {
      name: form.value.name,
      client: form.value.client,
      status: form.value.status
    })
  } else {
    projectStore.addProject({
      name: form.value.name,
      client: form.value.client,
      status: form.value.status
    })
  }
  closeDialog()
}

function closeDialog() {
  form.value = { id: null, name: '', client: '', status: '' }
  dialog.value = false
}
</script>
<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12">
        <v-sheet height="650">
          <v-calendar
            ref="calendar"
            v-model="selectedDate"
            :events="tasks"
            :event-color="getColor"
            @click:date="openTaskDialog"
          />
        </v-sheet>
      </v-col>
    </v-row>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>Add Task on {{ selectedDate }}</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="addTask">
            <v-text-field v-model="form.title" label="Task Title" />
            <v-select
              v-model="form.status"
              :items="['Scheduled', 'In Progress', 'Done']"
              label="Status"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="addTask">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const dialog = ref(false)
const selectedDate = ref('')
const form = ref({ title: '', status: 'Scheduled' })
const tasks = ref<Array<{ name: string, start: string, color: string }>>([])

function getColor(event) {
  switch (event.status) {
    case 'Done':
      return 'green'
    case 'In Progress':
      return 'orange'
    default:
      return 'blue'
  }
}

function openTaskDialog({ date }) {
  selectedDate.value = date
  dialog.value = true
}

function addTask() {
  tasks.value.push({
    name: form.value.title,
    start: selectedDate.value,
    status: form.value.status,
    color: getColor({ status: form.value.status })
  })
  closeDialog()
}

function closeDialog() {
  form.value = { title: '', status: 'Scheduled' }
  dialog.value = false
}
</script>
import { defineStore } from 'pinia'

export const useClientStore = defineStore('clients', {
  state: () => ({
    clients: [] as Array<{
      id: number,
      name: string,
      company: string,
      email: string,
      phone: string
    }>
  }),
  actions: {
    addClient(client) {
      const id = Date.now()
      this.clients.push({ id, ...client })
    },
    updateClient(id, updated) {
      const index = this.clients.findIndex(c => c.id === id)
      if (index !== -1) this.clients[index] = { id, ...updated }
    },
    deleteClient(id) {
      this.clients = this.clients.filter(c => c.id !== id)
    },
    getClient(id) {
      return this.clients.find(c => c.id === id)
    }
  }
})
<template>
  <v-container>
    <v-btn color="primary" @click="dialog = true">Add Client</v-btn>

    <v-data-table
      :items="clientStore.clients"
      :headers="headers"
      class="mt-4"
    >
      <template #item.actions="{ item }">
        <v-btn icon @click="edit(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="clientStore.deleteClient(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>{{ form.id ? 'Edit' : 'Add' }} Client</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="save">
            <v-text-field v-model="form.name" label="Contact Name" />
            <v-text-field v-model="form.company" label="Company" />
            <v-text-field v-model="form.email" label="Email" />
            <v-text-field v-model="form.phone" label="Phone Number" />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn @click="save" color="primary">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { useClientStore } from '@/stores/clients'

definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const clientStore = useClientStore()

const dialog = ref(false)
const form = ref({ id: null, name: '', company: '', email: '', phone: '' })

const headers = [
  { title: 'Name', key: 'name' },
  { title: 'Company', key: 'company' },
  { title: 'Email', key: 'email' },
  { title: 'Phone', key: 'phone' },
  { title: 'Actions', key: 'actions', sortable: false }
]

function edit(client) {
  form.value = { ...client }
  dialog.value = true
}

function save() {
  if (form.value.id) {
    clientStore.updateClient(form.value.id, {
      name: form.value.name,
      company: form.value.company,
      email: form.value.email,
      phone: form.value.phone
    })
  } else {
    clientStore.addClient({
      name: form.value.name,
      company: form.value.company,
      email: form.value.email,
      phone: form.value.phone
    })
  }
  closeDialog()
}

function closeDialog() {
  form.value = { id: null, name: '', company: '', email: '', phone: '' }
  dialog.value = false
}
</script>
import { defineStore } from 'pinia'

export const useEstimateStore = defineStore('estimates', {
  state: () => ({
    estimates: [] as Array<{
      id: number,
      client: string,
      title: string,
      items: Array<{ description: string, quantity: number, unitCost: number }>
    }>
  }),
  actions: {
    addEstimate(estimate) {
      const id = Date.now()
      this.estimates.push({ id, ...estimate })
    },
    updateEstimate(id, updated) {
      const index = this.estimates.findIndex(e => e.id === id)
      if (index !== -1) this.estimates[index] = { id, ...updated }
    },
    deleteEstimate(id) {
      this.estimates = this.estimates.filter(e => e.id !== id)
    },
    getEstimate(id) {
      return this.estimates.find(e => e.id === id)
    }
  }
})
<template>
  <v-container>
    <v-btn color="primary" @click="dialog = true">New Estimate</v-btn>

    <v-data-table
      :items="estimateStore.estimates"
      :headers="headers"
      class="mt-4"
    >
      <template #item.total="{ item }">
        ${{ getTotal(item.items).toFixed(2) }}
      </template>

      <template #item.actions="{ item }">
        <v-btn icon @click="edit(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="estimateStore.deleteEstimate(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <!-- Estimate Form Dialog -->
    <v-dialog v-model="dialog" max-width="700">
      <v-card>
        <v-card-title>{{ form.id ? 'Edit' : 'Add' }} Estimate</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="save">
            <v-text-field v-model="form.client" label="Client Name" />
            <v-text-field v-model="form.title" label="Estimate Title" />

            <v-divider class="my-3" />
            <h3>Line Items</h3>

            <v-row v-for="(item, index) in form.items" :key="index" class="mb-2">
              <v-col cols="5">
                <v-text-field v-model="item.description" label="Description" />
              </v-col>
              <v-col cols="3">
                <v-text-field type="number" v-model.number="item.quantity" label="Qty" />
              </v-col>
              <v-col cols="3">
                <v-text-field type="number" v-model.number="item.unitCost" label="Unit Cost" />
              </v-col>
              <v-col cols="1">
                <v-btn icon @click="removeItem(index)">
                  <v-icon>mdi-close</v-icon>
                </v-btn>
              </v-col>
            </v-row>

            <v-btn small color="secondary" @click="addItem">Add Item</v-btn>
            <v-divider class="my-4" />

            <p><strong>Total: </strong>${{ getTotal(form.items).toFixed(2) }}</p>
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="save">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { useEstimateStore } from '@/stores/estimates'

definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const estimateStore = useEstimateStore()

const dialog = ref(false)
const form = ref({
  id: null,
  client: '',
  title: '',
  items: []
})

const headers = [
  { title: 'Client', key: 'client' },
  { title: 'Title', key: 'title' },
  { title: 'Total', key: 'total' },
  { title: 'Actions', key: 'actions', sortable: false }
]

function getTotal(items) {
  return items.reduce((sum, item) => sum + item.quantity * item.unitCost, 0)
}

function addItem() {
  form.value.items.push({ description: '', quantity: 1, unitCost: 0 })
}

function removeItem(index) {
  form.value.items.splice(index, 1)
}

function edit(estimate) {
  form.value = { ...estimate, items: [...estimate.items] }
  dialog.value = true
}

function save() {
  if (form.value.id) {
    estimateStore.updateEstimate(form.value.id, {
      client: form.value.client,
      title: form.value.title,
      items: form.value.items
    })
  } else {
    estimateStore.addEstimate({
      client: form.value.client,
      title: form.value.title,
      items: form.value.items
    })
  }
  closeDialog()
}

function closeDialog() {
  form.value = { id: null, client: '', title: '', items: [] }
  dialog.value = false
}
</script>
import { defineStore } from 'pinia'

export const useInvoiceStore = defineStore('invoices', {
  state: () => ({
    invoices: [] as Array<{
      id: number,
      client: string,
      date: string,
      items: Array<{ description: string, quantity: number, unitPrice: number }>,
      status: 'Paid' | 'Unpaid'
    }>
  }),
  actions: {
    addInvoice(invoice) {
      const id = Date.now()
      this.invoices.push({ id, ...invoice })
    },
    updateInvoice(id, updated) {
      const index = this.invoices.findIndex(i => i.id === id)
      if (index !== -1) this.invoices[index] = { id, ...updated }
    },
    deleteInvoice(id) {
      this.invoices = this.invoices.filter(i => i.id !== id)
    },
    getInvoice(id) {
      return this.invoices.find(i => i.id === id)
    }
  }
})
<template>
  <v-container>
    <v-btn color="primary" @click="dialog = true">New Invoice</v-btn>

    <v-data-table
      :items="invoiceStore.invoices"
      :headers="headers"
      class="mt-4"
    >
      <template #item.total="{ item }">
        ${{ getTotal(item.items).toFixed(2) }}
      </template>

      <template #item.status="{ item }">
        <v-chip :color="item.status === 'Paid' ? 'green' : 'red'" dark>
          {{ item.status }}
        </v-chip>
      </template>

      <template #item.actions="{ item }">
        <v-btn icon @click="edit(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="invoiceStore.deleteInvoice(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="700">
      <v-card>
        <v-card-title>{{ form.id ? 'Edit' : 'Add' }} Invoice</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="save">
            <v-text-field v-model="form.client" label="Client Name" />
            <v-text-field v-model="form.date" label="Invoice Date" type="date" />
            <v-select
              v-model="form.status"
              :items="['Unpaid', 'Paid']"
              label="Status"
            />

            <v-divider class="my-4" />
            <h3>Line Items</h3>

            <v-row v-for="(item, index) in form.items" :key="index" class="mb-2">
              <v-col cols="5">
                <v-text-field v-model="item.description" label="Description" />
              </v-col>
              <v-col cols="3">
                <v-text-field type="number" v-model.number="item.quantity" label="Qty" />
              </v-col>
              <v-col cols="3">
                <v-text-field type="number" v-model.number="item.unitPrice" label="Unit Price" />
              </v-col>
              <v-col cols="1">
                <v-btn icon @click="removeItem(index)">
                  <v-icon>mdi-close</v-icon>
                </v-btn>
              </v-col>
            </v-row>

            <v-btn small color="secondary" @click="addItem">Add Item</v-btn>
            <v-divider class="my-4" />

            <p><strong>Total Due: </strong>${{ getTotal(form.items).toFixed(2) }}</p>
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="save">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { useInvoiceStore } from '@/stores/invoices'

definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const invoiceStore = useInvoiceStore()

const dialog = ref(false)
const form = ref({
  id: null,
  client: '',
  date: '',
  status: 'Unpaid',
  items: []
})

const headers = [
  { title: 'Client', key: 'client' },
  { title: 'Date', key: 'date' },
  { title: 'Status', key: 'status' },
  { title: 'Total Due', key: 'total' },
  { title: 'Actions', key: 'actions', sortable: false }
]

function getTotal(items) {
  return items.reduce((sum, item) => sum + item.quantity * item.unitPrice, 0)
}

function addItem() {
  form.value.items.push({ description: '', quantity: 1, unitPrice: 0 })
}

function removeItem(index) {
  form.value.items.splice(index, 1)
}

function edit(invoice) {
  form.value = { ...invoice, items: [...invoice.items] }
  dialog.value = true
}

function save() {
  if (form.value.id) {
    invoiceStore.updateInvoice(form.value.id, {
      client: form.value.client,
      date: form.value.date,
      status: form.value.status,
      items: form.value.items
    })
  } else {
    invoiceStore.addInvoice({
      client: form.value.client,
      date: form.value.date,
      status: form.value.status,
      items: form.value.items
    })
  }
  closeDialog()
}

function closeDialog() {
  form.value = { id: null, client: '', date: '', status: 'Unpaid', items: [] }
  dialog.value = false
}
</script>
import { defineStore } from 'pinia'

export const useTimeStore = defineStore('time', {
  state: () => ({
    logs: [] as Array<{
      id: number,
      employee: string,
      date: string,
      hours: number,
      description: string,
      project?: string
    }>
  }),
  actions: {
    addLog(log) {
      const id = Date.now()
      this.logs.push({ id, ...log })
    },
    deleteLog(id) {
      this.logs = this.logs.filter(l => l.id !== id)
    }
  }
})
<template>
  <v-container>
    <v-btn color="primary" @click="dialog = true">Log Time</v-btn>

    <v-data-table
      :items="timeStore.logs"
      :headers="headers"
      class="mt-4"
    >
      <template #item.actions="{ item }">
        <v-btn icon @click="timeStore.deleteLog(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>New Time Log</v-card-title>
        <v-card-text>
          <v-form @submit.prevent="save">
            <v-text-field v-model="form.employee" label="Employee Name" />
            <v-text-field v-model="form.project" label="Project Name (optional)" />
            <v-text-field v-model="form.date" label="Date" type="date" />
            <v-text-field type="number" v-model.number="form.hours" label="Hours Worked" />
            <v-textarea v-model="form.description" label="Task Description" rows="3" />
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="save">Save</v-btn>
          <v-btn @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { useTimeStore } from '@/stores/time'

definePageMeta({
  layout: 'dashboard',
  middleware: 'auth'
})

const timeStore = useTimeStore()

const dialog = ref(false)
const form = ref({
  employee: '',
  project: '',
  date: '',
  hours: 0,
  description: ''
})

const headers = [
  { title: 'Employee', key: 'employee' },
  { title: 'Project', key: 'project' },
  { title: 'Date', key: 'date' },
  { title: 'Hours', key: 'hours' },
  { title: 'Description', key: 'description' },
  { title: 'Actions', key: 'actions', sortable: false }
]

function save() {
  timeStore.addLog({ ...form.value })
  closeDialog()
}

function closeDialog() {
  form.value = {
    employee: '',
    project: '',
    date: '',
    hours: 0,
    description: ''
  }
  dialog.value = false
}
</script>
