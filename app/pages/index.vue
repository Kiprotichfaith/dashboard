<template>
  <div class="min-h-screen bg-slate-950 text-slate-100 overflow-x-hidden font-sans">
    <component :is="'script'" src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></component>
    <component :is="'script'" src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.25/jspdf.plugin.autotable.min.js"></component>

    <nav class="fixed top-0 w-full z-50 bg-slate-900/90 backdrop-blur-xl border-b border-slate-800 px-6 h-16 print:hidden">
      <div class="max-w-7xl mx-auto h-full flex justify-between items-center">
        <div class="flex items-center gap-3 shrink-0">
          <img src="/images/kvdalogo.png" alt="KVDA Logo" class="h-10 w-auto object-contain" />
          <span class="text-lg font-black italic tracking-tighter uppercase text-white border-l border-slate-800 pl-3">Personnel Portal</span>
        </div>
        <NavBar />
      </div>
    </nav>

    <div v-if="!isLoggedIn" class="relative z-0 min-h-screen flex items-center justify-center px-6 pt-16">
      <div class="max-w-3xl w-full bg-slate-900/50 border border-slate-800 p-12 rounded-3xl backdrop-blur-md text-center space-y-8 animate-in fade-in zoom-in duration-700">
        <div class="w-20 h-20 bg-cyan-600/10 rounded-full flex items-center justify-center mx-auto border border-cyan-500/20">
          <span class="text-3xl">🔒</span>
        </div>
        <h2 class="text-4xl md:text-5xl font-black text-white tracking-tighter italic uppercase">Restricted Portal</h2>
        <p class="text-slate-400 leading-relaxed text-lg">Identity authentication required. Use the <span class="text-cyan-400 font-bold">Access Portal</span> in the top navigation bar.</p>
      </div>
    </div>

    <main v-else class="max-w-7xl mx-auto p-6 pt-28 space-y-6 animate-in fade-in slide-in-from-bottom-4 duration-700">
      <div class="flex flex-wrap items-center justify-between gap-4 bg-slate-900/80 p-4 rounded-2xl border border-slate-800 backdrop-blur-sm">
        <div class="flex items-center gap-4">
          <div class="h-10 w-1 bg-cyan-500 rounded-full"></div>
          <div>
            <p class="text-[10px] text-slate-500 font-black uppercase tracking-[0.2em]">System Status</p>
            <p class="text-sm font-bold text-white uppercase tracking-tight">Active Session: {{ currentTime }}</p>
          </div>
        </div>
        <div class="flex gap-3">
          <button v-if="selectedStaff.length > 0" @click="bulkDelete" class="bg-red-500/10 border border-red-500/50 text-red-500 px-5 py-2 rounded-xl font-black text-xs uppercase tracking-widest transition-all hover:bg-red-500 hover:text-white">
            🗑️ Delete Selected ({{ selectedStaff.length }})
          </button>
          <button @click="generatePDF" class="flex items-center gap-2 bg-red-600 hover:bg-red-500 text-white px-5 py-2 rounded-xl font-black text-xs uppercase tracking-widest transition-all shadow-lg shadow-red-900/20">
            <span>📄</span> Download PDF
          </button>
          <button @click="exportToCSV" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 px-4 py-2 rounded-xl border border-slate-700 transition-all text-xs font-bold uppercase tracking-widest">
            📥 Export CSV
          </button>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-2xl flex flex-col">
          <div class="flex justify-between items-center mb-6">
            <p class="text-xs text-slate-500 uppercase font-black tracking-widest">Status Distribution</p>
          </div>
          <div class="w-full h-[240px]">
            <StatusChart :staff="staff" />
          </div>
        </div>

        <div class="lg:col-span-2 grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div class="bg-slate-900 border border-slate-800 rounded-3xl p-8 hover:border-green-500/30 transition-all group">
            <p class="text-xs text-slate-500 uppercase font-black">Active</p>
            <p class="text-5xl font-black text-green-400 mt-2">{{ activeCount }}</p>
          </div>
          <div class="bg-slate-900 border border-slate-800 rounded-3xl p-8 hover:border-slate-500/30 transition-all">
            <p class="text-xs text-slate-500 uppercase font-black">Inactive</p>
            <p class="text-5xl font-black text-cyan-400 mt-2">{{ inactiveCount }}</p>
          </div>
          <div class="bg-slate-900 border border-slate-800 rounded-3xl p-8 hover:border-red-500/30 transition-all">
            <p class="text-xs text-slate-500 uppercase font-black">Exited</p>
            <p class="text-5xl font-black text-red-500 mt-2">{{ exitedCount }}</p>
          </div>
          <div class="bg-slate-900 border border-slate-800 rounded-3xl p-8 hover:border-blue-500/30 transition-all">
            <p class="text-xs text-slate-500 uppercase font-black">Retired</p>
            <p class="text-5xl font-black text-blue-400 mt-2">{{ retiredCount }}</p>
          </div>
        </div>
      </div>

      <div class="bg-slate-900 border border-slate-800 rounded-2xl p-3 flex flex-col md:flex-row gap-4">
        <div class="relative flex-1">
          <span class="absolute left-4 top-1/2 -translate-y-1/2 opacity-40">🔍</span>
          <input v-model="search" placeholder="Search name, PF, or department..." class="w-full bg-slate-950 text-white rounded-xl pl-12 pr-4 py-4 outline-none focus:ring-2 focus:ring-cyan-500 border border-slate-800 transition-all" />
        </div>
        <select v-model="filterDept" class="bg-slate-950 text-white rounded-xl px-6 py-4 outline-none border border-slate-800 font-bold text-xs uppercase tracking-widest">
          <option value="">All Departments</option>
          <option>ICT</option>
          <option>FINANCE</option>
          <option>SUPPLY CHAIN MANAGEMENT</option>
          <option>ADMINISTRATION AND PLANNING</option>
        </select>
        <select v-model="status" class="bg-slate-950 text-white rounded-xl px-6 py-4 outline-none border border-slate-800 font-bold text-xs uppercase tracking-widest">
          <option value="">Status: All</option>
          <option>Active</option>
          <option>Inactive</option>
          <option>Retired</option>
          <option>Exited</option>
        </select>
      </div>

      <div class="overflow-hidden bg-slate-900 border border-slate-800 rounded-3xl shadow-2xl">
        <div class="overflow-x-auto">
          <table class="min-w-full text-sm">
            <thead class="bg-slate-800/50 text-slate-500 text-[10px] uppercase font-black tracking-[0.2em] border-b border-slate-800">
              <tr>
                <th class="p-6 text-left w-12">
                   <input type="checkbox" @change="toggleAllSelection" :checked="isAllSelected" class="accent-cyan-500" />
                </th>
                <th class="p-6 text-left">#</th>
                <th class="p-6 text-left">Staff Member</th>
                <th class="p-6 text-left">PF Number</th>
                <th class="p-6 text-left">Email Address</th>
                <th class="p-6 text-left">Department</th>
                <th class="p-6 text-left">Workstation</th>
                <th class="p-6 text-left">Status</th>
                <th class="p-6 text-right">Actions</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-800/50">
              <tr v-for="(s, index) in filteredStaff" :key="s.pf" 
                  class="hover:bg-cyan-500/5 transition-all group"
                  :class="{'bg-cyan-500/5': selectedStaff.includes(s.pf)}">
                <td class="p-6">
                  <input type="checkbox" v-model="selectedStaff" :value="s.pf" class="accent-cyan-500" />
                </td>
                <td class="p-6 text-slate-500 font-mono">{{ index + 1 }}</td>
                <td class="p-6">
                  <p class="font-bold text-white group-hover:text-cyan-400 transition-colors" v-html="highlight(s.surname + ' ' + s.firstname)"></p>
                </td>
                <td class="p-6 font-mono text-slate-400" v-html="highlight(s.pf)"></td>
                <td class="p-6 text-slate-300">{{ s.email }}</td>
                <td class="p-6 text-slate-500 font-black uppercase text-[10px]">{{ s.department }}</td> 
                <td class="p-6 text-slate-500 font-black uppercase text-[10px]">{{ s.workstation }}</td> 
                <td class="p-6">
                  <span class="px-3 py-1 rounded-lg text-[10px] font-black uppercase tracking-tighter border" :class="statusStyles(s.status)">
                    {{ s.status }}
                  </span>
                </td>
                <td class="p-6 text-right">
                  <button @click="openDrawer(s)" class="bg-slate-800 p-2 px-4 rounded-lg hover:bg-cyan-600 hover:text-black transition-all text-[10px] font-black uppercase">
                    View Profile
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <AddStaffForm @add="addOrUpdateStaff" @update="addOrUpdateStaff" :editData="editForm" id="staff-form" />
    </main>

    <Teleport to="body">
        <div v-if="isDrawerOpen" class="fixed inset-0 z-[1000] overflow-hidden">
          <div class="absolute inset-0 bg-black/60 backdrop-blur-sm" @click="isDrawerOpen = false"></div>
          <div class="absolute right-0 top-0 h-full w-full max-w-md bg-slate-900 border-l border-slate-800 shadow-2xl p-8 animate-in slide-in-from-right duration-300 overflow-y-auto">
            <div class="flex justify-between items-center mb-8">
              <h2 class="text-2xl font-black italic text-white uppercase tracking-tighter">Personnel File</h2>
              <button @click="isDrawerOpen = false" class="text-slate-500 hover:text-white text-2xl">✕</button>
            </div>
  
            <div v-if="drawerData" class="space-y-6">
              <div class="flex items-center gap-6 p-6 bg-slate-800/50 rounded-3xl border border-slate-700">
                <div class="w-20 h-20 bg-cyan-600 rounded-2xl flex items-center justify-center text-3xl font-black text-black">
                  {{ drawerData.firstname[0] }}{{ drawerData.surname[0] }}
                </div>
                <div>
                  <p class="text-2xl font-black text-white">{{ drawerData.firstname }} {{ drawerData.surname }}</p>
                  <p class="text-cyan-500 font-bold uppercase tracking-widest text-xs">{{ drawerData.pf }}</p>
                </div>
              </div>
  
              <div class="flex items-center justify-between p-4 bg-slate-950 rounded-2xl border border-slate-800">
                <div>
                  <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest">Credential Health</p>
                  <p class="text-sm font-bold" :class="getSecurityStatus(drawerData.lastReset).color">
                    {{ getSecurityStatus(drawerData.lastReset).label }}
                  </p>
                </div>
                <div class="text-right">
                  <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest">Last Reset</p>
                  <p class="text-sm text-white font-mono">{{ drawerData.lastReset }}</p>
                </div>
              </div>
  
              <div class="p-6 bg-slate-950/50 rounded-3xl border border-slate-800 space-y-4">
                <p class="text-[10px] text-slate-400 font-black uppercase tracking-[0.2em]">Security Credentials</p>
                <div class="space-y-1">
                  <label class="text-[9px] text-slate-500 uppercase font-bold ml-1">Set New Staff Password</label>
                  <div class="flex gap-2">
                    <input v-model="newStaffPassword" type="password" placeholder="Enter permanent password" class="flex-1 px-4 py-3 rounded-xl bg-slate-900 text-white border border-slate-700 focus:ring-1 focus:ring-cyan-500 outline-none transition-all text-xs" />
                    <button @click="updateStaffPassword" class="bg-cyan-600 px-4 py-3 rounded-xl text-black font-black text-[10px] uppercase hover:bg-cyan-500 transition-all">
                      Update
                    </button>
                  </div>
                </div>
              </div>
  
              <div class="grid grid-cols-1 gap-4 text-sm">
                 <div class="flex justify-between border-b border-slate-800/50 pb-2">
                   <span class="text-slate-500 uppercase font-black text-[10px]">Workstation</span>
                   <span class="text-white font-bold">{{ drawerData.workstation }}</span>
                 </div>
                 <div class="flex justify-between border-b border-slate-800/50 pb-2">
                   <span class="text-slate-500 uppercase font-black text-[10px]">Join Date</span>
                   <span class="text-white font-bold">{{ drawerData.joinedDate }}</span>
                 </div>
              </div>
  
              <div class="space-y-3 pt-6">
                <button @click="editStaff(drawerData)" class="w-full py-4 bg-slate-800 text-white font-black rounded-2xl hover:bg-slate-700 transition-all uppercase tracking-widest text-xs">Update Information</button>
                <button @click="confirmDelete(drawerData.pf)" class="w-full py-4 bg-red-900/20 text-red-500 font-black rounded-2xl hover:bg-red-900/40 transition-all uppercase tracking-widest text-xs border border-red-900/50">Purge Data</button>
              </div>
            </div>
          </div>
        </div>
    </Teleport>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const isLoggedIn = ref(false)
const search = ref('')
const status = ref('')
const filterDept = ref('')
const selectedStaff = ref([])
const isDrawerOpen = ref(false)
const drawerData = ref(null)
const currentTime = ref('')
const editForm = ref(null)
const newStaffPassword = ref('')

// Staff Data
const staff = ref([
  { surname: 'KIPTOO', firstname: 'Faith', email: 'f.kiptoo@kvda.go.ke', pf: 'KV001', department: 'ICT', workstation: 'ELDORET', status: 'Active', lastReset: '2025-11-20', joinedDate: '2022-04-12', password: 'password123' },
  { surname: 'OCHIENG', firstname: 'Brian', email: 'b.ochieng@kvda.go.ke', pf: 'KV002', department: 'FINANCE', workstation: 'TURKANA', status: 'Active', lastReset: '2025-10-15', joinedDate: '2021-08-30', password: 'password123' },
  { surname: 'WANJIRU', firstname: 'Grace', email: 'g.wanjiru@kvda.go.ke', pf: 'KV003', department: 'SUPPLY CHAIN MANAGEMENT', workstation: 'MARAKWET', status: 'Inactive', lastReset: '2024-05-10', joinedDate: '2020-01-05', password: 'password123' },
  { surname: 'KIBET', firstname: 'Allan', email: 'a.kibet@kvda.go.ke', pf: 'KV004', department: 'ADMINISTRATION AND PLANNING', workstation: 'ELDORET', status: 'Exited', lastReset: '2025-12-01', joinedDate: '2023-11-12', password: 'password123' },
  { surname: 'CHEMUTAI', firstname: 'Mercy', email: 'm.chemutai@kvda.go.ke', pf: 'KV005', department: 'ICT', workstation: 'MARAKWET', status: 'Active', lastReset: '2025-08-20', joinedDate: '2022-06-15', password: 'password123' },
  { surname: 'MWANGI', firstname: 'James', email: 'j.mwangi@kvda.go.ke', pf: 'KV006', department: 'FINANCE', workstation: 'ELDORET', status: 'Retired', lastReset: '2024-01-10', joinedDate: '2019-05-20', password: 'password123' },
  { surname: 'ANYANGO', firstname: 'Sarah', email: 's.anyango@kvda.go.ke', pf: 'KV007', department: 'SUPPLY CHAIN MANAGEMENT', workstation: 'TURKANA', status: 'Active', lastReset: '2025-11-05', joinedDate: '2023-01-10', password: 'password123' },
  { surname: 'KOECH', firstname: 'David', email: 'd.koech@kvda.go.ke', pf: 'KV008', department: 'ADMINISTRATION AND PLANNING', workstation: 'MARAKWET', status: 'Retired', lastReset: '2024-03-25', joinedDate: '2018-11-12', password: 'password123' },
  { surname: 'BARASA', firstname: 'Emmanuel', email: 'e.barasa@kvda.go.ke', pf: 'KV009', department: 'ICT', workstation: 'TURKANA', status: 'Active', lastReset: '2025-12-10', joinedDate: '2023-07-22', password: 'password123' },
  { surname: 'REHEMA', firstname: 'Fatuma', email: 'f.rehema@kvda.go.ke', pf: 'KV010', department: 'FINANCE', workstation: 'ELDORET', status: 'Active', lastReset: '2025-11-30', joinedDate: '2022-10-05', password: 'password123' }
])

onMounted(() => {
  if (process.client) {
    isLoggedIn.value = localStorage.getItem('loggedIn') === 'true'
    setInterval(() => { currentTime.value = new Date().toLocaleTimeString() }, 1000)
  }
})

// RE-WRITTEN GENERATE PDF FUNCTION (Updated with index numbering)
function generatePDF() {
  const { jsPDF } = window.jspdf;
  if (!jsPDF) {
    alert("System library loading. Please try again in 2 seconds.");
    return;
  }

  const doc = new jsPDF('p', 'mm', 'a4');

  // Header Design
  doc.setFillColor(8, 145, 178); // Cyan-600
  doc.rect(0, 0, 210, 40, 'F');
  
  doc.setFont("helvetica", "bold");
  doc.setFontSize(22);
  doc.setTextColor(255, 255, 255);
  doc.text("KVDA PERSONNEL REGISTRY", 105, 20, { align: "center" });
  
  doc.setFontSize(10);
  doc.setFont("helvetica", "normal");
  doc.text("ICT & Network Administration Division Official Report", 105, 28, { align: "center" });

  // Prep Table Data with numbering
  const tableData = filteredStaff.value.map((s, i) => [
    i + 1,
    `${s.surname} ${s.firstname}`,
    s.pf,
    s.email,
    s.department,
    s.workstation,
    s.status
  ]);

  // Execute Table Generation
  doc.autoTable({
    startY: 50,
    head: [['#', 'Name', 'PF No.', 'Email Address', 'Department', 'Station', 'Status']],
    body: tableData,
    theme: 'grid',
    headStyles: { fillColor: [15, 23, 42], textColor: [255, 255, 255], fontStyle: 'bold' },
    styles: { fontSize: 7, cellPadding: 3 },
    alternateRowStyles: { fillColor: [241, 245, 249] },
    margin: { left: 10, right: 10 }
  });

  // Footer
  const pageCount = doc.internal.getNumberOfPages();
  for(let i = 1; i <= pageCount; i++) {
    doc.setPage(i);
    doc.setFontSize(8);
    doc.setTextColor(150);
    doc.text(`Official Document - Generated on ${new Date().toLocaleDateString()} - Page ${i} of ${pageCount}`, 105, 285, { align: "center" });
  }

  doc.save(`KVDA_Personnel_Report_${Date.now()}.pdf`);
}

// Logic functions
function highlight(text) {
  if (!search.value) return text
  const re = new RegExp(search.value, 'gi')
  return text.toString().replace(re, `<mark class="bg-cyan-500/30 text-cyan-200 rounded px-1">${search.value}</mark>`)
}

function getSecurityStatus(dateString) {
  if (!dateString) return { label: 'UNKNOWN', color: 'text-slate-500' }
  const lastDate = new Date(dateString);
  const now = new Date();
  const diffDays = Math.ceil(Math.abs(now - lastDate) / (1000 * 60 * 60 * 24));
  if (diffDays > 180) return { label: 'CRITICAL', color: 'text-red-500' };
  if (diffDays > 90) return { label: 'STALE', color: 'text-yellow-500' };
  return { label: 'SECURE', color: 'text-green-500' };
}

const activeCount = computed(() => staff.value.filter(s => s.status === 'Active').length)
const exitedCount = computed(() => staff.value.filter(s => s.status === 'Exited').length)
const inactiveCount = computed(() => staff.value.filter(s => s.status === 'Inactive').length)
const retiredCount = computed(() => staff.value.filter(s => s.status === 'Retired').length)

const filteredStaff = computed(() => {
  return staff.value.filter(s => {
    const matchesStatus = !status.value || s.status === status.value
    const matchesDept = !filterDept.value || s.department === filterDept.value
    const fullName = (s.surname + ' ' + s.firstname).toLowerCase()
    const searchLow = search.value.toLowerCase()
    return matchesStatus && matchesDept && (
      fullName.includes(searchLow) || 
      s.pf.toLowerCase().includes(searchLow) ||
      s.department.toLowerCase().includes(searchLow)
    )
  })
})

const isAllSelected = computed(() => selectedStaff.value.length === filteredStaff.value.length && filteredStaff.value.length > 0)
function toggleAllSelection() {
  selectedStaff.value = isAllSelected.value ? [] : filteredStaff.value.map(s => s.pf)
}

function openDrawer(data) {
  drawerData.value = data
  isDrawerOpen.value = true
}

function statusStyles(val) {
  switch (val) {
    case 'Active': return 'bg-green-500/10 text-green-400 border-green-500/20'
    case 'Inactive': return 'bg-slate-500/10 text-slate-400 border-slate-500/20'
    case 'Retired': return 'bg-blue-500/10 text-blue-400 border-blue-500/20'
    case 'Exited': return 'bg-red-500/10 text-red-400 border-red-500/20'
    default: return 'bg-blue-500/10 text-blue-400 border-blue-500/20'
  }
}

// ADDED NEW CORE FUNCTIONS FOR DELETE AND EDIT
function bulkDelete() {
  if (confirm(`Permanent Action: Delete ${selectedStaff.value.length} records?`)) {
    staff.value = staff.value.filter(s => !selectedStaff.value.includes(s.pf))
    selectedStaff.value = []
  }
}

function confirmDelete(pf) {
  if (confirm('Are you sure you want to purge this record?')) {
    staff.value = staff.value.filter(s => s.pf !== pf)
    isDrawerOpen.value = false
  }
}

function editStaff(staffData) {
  editForm.value = { ...staffData }
  isDrawerOpen.value = false
  // Smooth scroll to form
  const el = document.getElementById('staff-form')
  if(el) el.scrollIntoView({ behavior: 'smooth' })
}

function addOrUpdateStaff(data) {
  const index = staff.value.findIndex(s => s.pf === data.pf)
  if (index !== -1) {
    staff.value[index] = { ...staff.value[index], ...data }
    alert('Record updated.')
  } else {
    staff.value.push({ ...data, password: 'password123', lastReset: new Date().toISOString().split('T')[0] })
    alert('New staff added.')
  }
  editForm.value = null
}

function updateStaffPassword() {
  if (!newStaffPassword.value) return;
  const staffMember = staff.value.find(s => s.pf === drawerData.value.pf);
  if (staffMember) {
    staffMember.password = newStaffPassword.value;
    staffMember.lastReset = new Date().toISOString().split('T')[0];
    drawerData.value.lastReset = staffMember.lastReset;
    alert('Credentials updated.');
    newStaffPassword.value = '';
  }
}

function exportToCSV() {
  const headers = 'No,Name,PF,Email,Station,Dept,Status\n'
  const rows = filteredStaff.value.map((s, i) => `${i+1},${s.surname} ${s.firstname},${s.pf},${s.email},${s.workstation},${s.department},${s.status}\n`).join('')
  const blob = new Blob([headers + rows], { type: 'text/csv' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'KVDA_Registry.csv'
  a.click()
}
</script>