<template>
  <div class="flex h-screen bg-[#0a0a0b] text-gray-200 font-sans">
    <!-- Command Menu -->
    <CommandMenu :show="showCommandMenu" @close="showCommandMenu = false" />

    <!-- Sidebar -->
    <aside v-show="sidebarOpen" class="w-56 shrink-0 border-r border-gray-800/50 flex flex-col">
      <!-- Logo -->
      <div class="p-4 flex items-center gap-3">
        <div
          class="w-7 h-7 rounded bg-linear-to-br from-indigo-500 to-purple-600 flex items-center justify-center text-xs font-bold text-white"
        >
          N
        </div>
        <span class="font-semibold text-white">Notta</span>
      </div>

      <!-- Navigation -->
      <nav class="px-2 space-y-0.5 flex-1">
        <router-link
          to="/"
          class="flex items-center gap-3 px-3 py-2 text-sm text-gray-400 hover:text-white hover:bg-gray-800/50 rounded-md"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M9 17V7m0 10a2 2 0 01-2 2H5a2 2 0 01-2-2V7a2 2 0 012-2h2a2 2 0 012 2m0 10a2 2 0 002 2h2a2 2 0 002-2M9 7a2 2 0 012-2h2a2 2 0 012 2m0 10V7m0 10a2 2 0 002 2h2a2 2 0 002-2V7a2 2 0 00-2-2h-2a2 2 0 00-2 2"
            />
          </svg>
          Kanban
        </router-link>
        <router-link
          to="/notes"
          class="flex items-center gap-3 px-3 py-2 text-sm text-white bg-gray-800/80 rounded-md"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"
            />
          </svg>
          Notes
        </router-link>
        <router-link
          to="/reminders"
          class="flex items-center gap-3 px-3 py-2 text-sm text-gray-400 hover:text-white hover:bg-gray-800/50 rounded-md"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"
            />
          </svg>
          Reminders
        </router-link>
      </nav>

      <!-- Bottom section -->
      <div class="p-3 border-t border-gray-800/50">
        <div class="flex items-center gap-2">
          <button class="p-2 hover:bg-gray-800 rounded text-gray-500 hover:text-gray-300">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"
              />
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
              />
            </svg>
          </button>
        </div>
      </div>
    </aside>

    <!-- Notes List Panel -->
    <div class="w-72 shrink-0 border-r border-gray-800/50 flex flex-col bg-[#0d0d0e]">
      <!-- List Header -->
      <div class="h-12 border-b border-gray-800/50 flex items-center justify-between px-4">
        <button
          class="p-1.5 hover:bg-gray-800 rounded text-gray-500 hover:text-gray-300"
          @click="sidebarOpen = !sidebarOpen"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 12h16M4 18h16"
            />
          </svg>
        </button>
        <div class="flex items-center gap-1">
          <button class="p-1.5 hover:bg-gray-800 rounded text-gray-500 hover:text-gray-300">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 6h16M4 10h16M4 14h16M4 18h16"
              />
            </svg>
          </button>
          <button
            class="p-1.5 hover:bg-gray-800 rounded text-gray-500 hover:text-gray-300"
            @click="addNote"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"
              />
            </svg>
          </button>
          <button
            class="p-1.5 hover:bg-gray-800 rounded text-gray-500 hover:text-red-400"
            @click="deleteSelectedNote"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
              />
            </svg>
          </button>
        </div>
      </div>

      <!-- Notes List -->
      <div class="flex-1 overflow-y-auto">
        <!-- Group by date -->
        <template v-for="group in groupedNotes" :key="group.label">
          <div class="px-4 py-2 text-xs text-gray-500 font-medium sticky top-0 bg-[#0d0d0e]">
            {{ group.label }}
          </div>
          <div
            v-for="note in group.notes"
            :key="note.id"
            class="px-4 py-3 border-b border-gray-800/30 cursor-pointer hover:bg-gray-800/30 transition-colors"
            :class="selectedNote?.id === note.id ? 'bg-gray-800/50' : ''"
            @click="selectNote(note)"
          >
            <h3 class="text-sm font-medium text-white truncate">
              {{ note.title || 'New Note' }}
            </h3>
            <div class="flex items-center gap-2 mt-1">
              <span class="text-xs text-gray-500">{{ formatDate(note.updatedAt) }}</span>
              <span class="text-xs text-gray-600 truncate flex-1">
                {{ note.content.substring(0, 50) || 'No additional text' }}
              </span>
            </div>
          </div>
        </template>
      </div>
    </div>

    <!-- Note Editor -->
    <main class="flex-1 flex flex-col overflow-hidden bg-[#0a0a0b]">
      <template v-if="selectedNote">
        <!-- Editor Header -->
        <div class="h-12 border-b border-gray-800/50 flex items-center justify-between px-4">
          <span class="text-xs text-gray-500">{{ formatFullDate(selectedNote.updatedAt) }}</span>
          <div class="flex items-center gap-2">
            <button class="p-1.5 hover:bg-gray-800 rounded text-gray-500 hover:text-gray-300">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                />
              </svg>
            </button>
          </div>
        </div>

        <!-- Editor Content -->
        <div class="flex-1 overflow-y-auto p-6">
          <input
            v-model="selectedNote.title"
            type="text"
            placeholder="Title"
            class="w-full text-2xl font-bold text-white bg-transparent border-none outline-none placeholder-gray-600 mb-4"
            @input="updateNote"
          />
          <textarea
            v-model="selectedNote.content"
            placeholder="Start writing..."
            class="w-full h-full text-gray-300 bg-transparent border-none outline-none placeholder-gray-600 resize-none text-sm leading-relaxed"
            @input="updateNote"
          ></textarea>
        </div>
      </template>

      <!-- Empty State -->
      <div v-else class="flex-1 flex items-center justify-center">
        <div class="text-center">
          <svg
            class="w-16 h-16 mx-auto text-gray-700 mb-4"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="1"
              d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"
            />
          </svg>
          <p class="text-gray-500 text-sm">Select a note or create a new one</p>
          <button
            class="mt-4 px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white text-sm rounded-lg transition-colors"
            @click="addNote"
          >
            New Note
          </button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import CommandMenu from '@/components/CommandMenu.vue'
import { computed, onMounted, onUnmounted, ref, watch } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

interface Note {
  id: string
  title: string
  content: string
  createdAt: Date
  updatedAt: Date
}

const showCommandMenu = ref(false)
const sidebarOpen = ref(localStorage.getItem('notta-sidebar-open') !== 'false')

watch(sidebarOpen, (isOpen) => {
  localStorage.setItem('notta-sidebar-open', String(isOpen))
})

const loadNotes = (): Note[] => {
  const saved = localStorage.getItem('notta-notes')
  if (saved) {
    try {
      const parsed = JSON.parse(saved)
      return parsed.map((note: Note) => ({
        ...note,
        createdAt: new Date(note.createdAt),
        updatedAt: new Date(note.updatedAt),
      }))
    } catch {
      return []
    }
  }
  return []
}

const notes = ref<Note[]>(loadNotes())
const selectedNote = ref<Note | null>(null)

watch(
  notes,
  (newNotes) => {
    localStorage.setItem('notta-notes', JSON.stringify(newNotes))
  },
  { deep: true },
)

const groupedNotes = computed(() => {
  const now = new Date()
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
  const yesterday = new Date(today.getTime() - 24 * 60 * 60 * 1000)
  const lastWeek = new Date(today.getTime() - 7 * 24 * 60 * 60 * 1000)

  const groups: { label: string; notes: Note[] }[] = []
  const sorted = [...notes.value].sort((a, b) => b.updatedAt.getTime() - a.updatedAt.getTime())

  const todayNotes: Note[] = []
  const yesterdayNotes: Note[] = []
  const lastWeekNotes: Note[] = []
  const olderByMonth: Record<string, Note[]> = {}

  sorted.forEach((note) => {
    const noteDate = new Date(
      note.updatedAt.getFullYear(),
      note.updatedAt.getMonth(),
      note.updatedAt.getDate(),
    )

    if (noteDate.getTime() >= today.getTime()) {
      todayNotes.push(note)
    } else if (noteDate.getTime() >= yesterday.getTime()) {
      yesterdayNotes.push(note)
    } else if (noteDate.getTime() >= lastWeek.getTime()) {
      lastWeekNotes.push(note)
    } else {
      const monthKey = note.updatedAt.toLocaleDateString('en-US', {
        month: 'long',
        year: 'numeric',
      })
      if (!olderByMonth[monthKey]) {
        olderByMonth[monthKey] = []
      }
      olderByMonth[monthKey].push(note)
    }
  })

  if (todayNotes.length > 0) {
    groups.push({ label: 'Today', notes: todayNotes })
  }
  if (yesterdayNotes.length > 0) {
    groups.push({ label: 'Yesterday', notes: yesterdayNotes })
  }
  if (lastWeekNotes.length > 0) {
    groups.push({ label: 'Previous 7 Days', notes: lastWeekNotes })
  }

  Object.entries(olderByMonth).forEach(([month, monthNotes]) => {
    groups.push({ label: month, notes: monthNotes })
  })

  return groups
})

const formatDate = (date: Date): string => {
  const now = new Date()
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
  const noteDate = new Date(date.getFullYear(), date.getMonth(), date.getDate())

  if (noteDate.getTime() === today.getTime()) {
    return date.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' })
  }
  return date.toLocaleDateString('en-US', { month: 'short', day: '2-digit' })
}

const formatFullDate = (date: Date): string => {
  return (
    date.toLocaleDateString('en-US', {
      day: 'numeric',
      month: 'long',
      year: 'numeric',
    }) +
    ' at ' +
    date.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' })
  )
}

const addNote = (): void => {
  const now = new Date()
  const newNote: Note = {
    id: `note-${Date.now()}`,
    title: '',
    content: '',
    createdAt: now,
    updatedAt: now,
  }
  notes.value.unshift(newNote)
  selectedNote.value = newNote
}

const selectNote = (note: Note): void => {
  selectedNote.value = note
}

const updateNote = (): void => {
  if (selectedNote.value) {
    selectedNote.value.updatedAt = new Date()
  }
}

const deleteSelectedNote = (): void => {
  if (selectedNote.value) {
    const index = notes.value.findIndex((n) => n.id === selectedNote.value?.id)
    notes.value = notes.value.filter((n) => n.id !== selectedNote.value?.id)
    // Select next note or previous if at end
    if (notes.value.length > 0) {
      const newIndex = Math.min(index, notes.value.length - 1)
      selectedNote.value = notes.value[newIndex] ?? null
    } else {
      selectedNote.value = null
    }
  }
}

let lastKeyTime = 0
let lastKey = ''

const handleKeydown = (e: KeyboardEvent): void => {
  const now = Date.now()

  // G + key shortcuts (must press within 500ms)
  if (lastKey === 'g' && now - lastKeyTime < 500) {
    if (e.key === 'n') {
      e.preventDefault()
      router.push('/notes')
      lastKey = ''
      return
    }
    if (e.key === 't') {
      e.preventDefault()
      router.push('/')
      lastKey = ''
      return
    }
    if (e.key === 'r') {
      e.preventDefault()
      router.push('/reminders')
      lastKey = ''
      return
    }
  }

  // Track 'g' key press
  if (e.key === 'g' && !e.ctrlKey && !e.metaKey) {
    lastKey = 'g'
    lastKeyTime = now
    return
  }

  if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
    e.preventDefault()
    showCommandMenu.value = !showCommandMenu.value
  }
  if ((e.ctrlKey || e.metaKey) && e.key === 'n') {
    e.preventDefault()
    addNote()
  }
}

onMounted(() => {
  if (notes.value.length > 0) {
    selectedNote.value = notes.value[0] ?? null
  }
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
})
</script>
