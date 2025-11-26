<template>
  <div class="flex h-screen bg-[#0a0a0b] text-gray-200 font-sans">
    <!-- Command Menu Modal -->
    <Teleport to="body">
      <Transition name="fade">
        <div
          v-if="showCommandMenu"
          class="fixed inset-0 z-50 flex items-start justify-center pt-[20vh]"
          @click="showCommandMenu = false"
        >
          <div class="absolute inset-0 bg-black/60 backdrop-blur-sm pointer-events-none"></div>
          <div
            class="relative w-full max-w-xl bg-[#1a1a1e] border border-gray-800 rounded-xl shadow-2xl overflow-hidden"
            @click.stop
          >
            <!-- Search Input -->
            <div class="flex items-center px-4 border-b border-gray-800/80">
              <svg
                class="w-4 h-4 text-gray-500"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                />
              </svg>
              <input
                ref="commandInput"
                v-model="commandSearch"
                type="text"
                placeholder="Type a command or search..."
                class="flex-1 px-3 py-4 bg-transparent text-sm text-white placeholder-gray-500 focus:outline-none"
                @keydown.down.prevent="navigateDown"
                @keydown.up.prevent="navigateUp"
                @keydown.enter.prevent="executeCommand"
                @keydown.escape="showCommandMenu = false"
              />
              <kbd
                class="hidden sm:flex items-center gap-1 px-2 py-1 text-[10px] text-gray-500 bg-gray-800/50 rounded"
              >
                ESC
              </kbd>
            </div>

            <!-- Command List -->
            <div class="max-h-80 overflow-y-auto py-2">
              <template v-for="(group, groupIndex) in filteredCommands" :key="group.name">
                <div v-if="group.commands.length > 0">
                  <div class="px-4 py-2 text-xs text-gray-500 font-medium">{{ group.name }}</div>
                  <div
                    v-for="(command, cmdIndex) in group.commands"
                    :key="command.id"
                    :class="[
                      'flex items-center gap-3 px-4 py-2.5 mx-2 rounded-lg cursor-pointer transition-colors',
                      isSelected(groupIndex, cmdIndex)
                        ? 'bg-gray-800/80 text-white'
                        : 'text-gray-400 hover:bg-gray-800/50 hover:text-white',
                    ]"
                    @click="executeCommand(command)"
                    @mouseenter="setSelected(groupIndex, cmdIndex)"
                  >
                    <component :is="command.icon" class="w-4 h-4 text-gray-500" />
                    <span class="flex-1 text-sm">{{ command.label }}</span>
                    <div v-if="command.shortcut" class="flex items-center gap-1">
                      <template v-for="(key, keyIndex) in command.shortcut" :key="keyIndex">
                        <span v-if="keyIndex > 0" class="text-[10px] text-gray-600">then</span>
                        <kbd
                          class="px-1.5 py-0.5 text-[10px] text-gray-400 bg-gray-800 rounded border border-gray-700"
                          >{{ key }}</kbd
                        >
                      </template>
                    </div>
                  </div>
                </div>
              </template>
              <div
                v-if="filteredCommands.every((g) => g.commands.length === 0)"
                class="px-4 py-8 text-center text-sm text-gray-500"
              >
                No commands found
              </div>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>

    <!-- Sidebar -->
    <aside
      v-show="sidebarOpen"
      class="w-56 flex-shrink-0 border-r border-gray-800/50 flex flex-col"
    >
      <!-- Logo -->
      <div class="p-4 flex items-center gap-3">
        <div
          class="w-7 h-7 rounded bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center text-xs font-bold text-white"
        >
          N
        </div>
        <span class="font-semibold text-white">Notta</span>
      </div>

      <!-- Navigation -->
      <nav class="px-2 space-y-0.5 flex-1">
        <router-link
          to="/"
          class="flex items-center gap-3 px-3 py-2 text-sm text-white bg-gray-800/80 rounded-md"
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
          class="flex items-center gap-3 px-3 py-2 text-sm text-gray-400 hover:text-white hover:bg-gray-800/50 rounded-md"
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

    <!-- Main Content -->
    <main class="flex-1 flex flex-col overflow-hidden">
      <!-- Header -->
      <header class="h-12 border-b border-gray-800/50 flex items-center px-4">
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
      </header>

      <!-- Board -->
      <div class="flex-1 overflow-auto p-4">
        <div class="flex gap-4 min-h-full">
          <!-- Columns -->
          <div
            v-for="column in columns"
            :key="column.id"
            class="w-72 flex-shrink-0 flex flex-col rounded-lg"
            @dragover.prevent
            @drop="handleDrop(column.id)"
          >
            <!-- Column Header with Glow -->
            <div
              class="px-3 py-3 flex items-center justify-between rounded-t-lg"
              :style="{
                background: `linear-gradient(180deg, ${column.glowColor}15 0%, #0a0a0b 100%)`,
                boxShadow: `0 0 20px ${column.glowColor}20`,
              }"
            >
              <div class="flex items-center gap-2">
                <div
                  class="w-4 h-4 rounded-full flex items-center justify-center"
                  :style="{
                    backgroundColor: column.glowColor + '30',
                    boxShadow: `0 0 8px ${column.glowColor}`,
                  }"
                >
                  <div
                    class="w-2 h-2 rounded-full"
                    :style="{ backgroundColor: column.glowColor }"
                  ></div>
                </div>
                <span class="font-medium text-sm text-white">{{ column.name }}</span>
                <span class="text-xs text-gray-500 ml-1">{{
                  getColumnTasks(column.id).length
                }}</span>
              </div>
              <button
                class="p-1 hover:bg-white/10 rounded text-gray-500 hover:text-white"
                @click="addTask(column.id)"
              >
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 4v16m8-8H4"
                  />
                </svg>
              </button>
            </div>

            <!-- Tasks -->
            <div class="space-y-2 p-2">
              <div
                v-for="task in getColumnTasks(column.id)"
                :key="task.id"
                draggable="true"
                @dragstart="handleDragStart(task)"
                @dragover.prevent="handleDragOver(task)"
                @dragleave="handleDragLeave"
                @drop.stop="handleTaskDrop(task)"
                :class="[
                  'group bg-[#151518] border rounded-lg p-3 cursor-grab hover:border-gray-700 transition-colors',
                  dragOverTaskId === task.id
                    ? 'border-indigo-500 border-t-2'
                    : 'border-gray-800/50',
                ]"
              >
                <!-- Task ID and Status -->
                <div class="flex items-center justify-between mb-2">
                  <div class="flex items-center gap-1">
                    <span class="text-[10px] text-gray-600 font-mono">{{ task.id }}</span>
                  </div>
                  <div
                    class="w-3 h-3 rounded-full flex items-center justify-center"
                    :style="{ backgroundColor: column.glowColor + '30' }"
                  >
                    <div
                      class="w-1.5 h-1.5 rounded-full"
                      :style="{ backgroundColor: column.glowColor }"
                    ></div>
                  </div>
                </div>

                <!-- Title -->
                <input
                  v-if="editingTaskId === task.id"
                  :ref="
                    (el) => {
                      if (el && editingTaskId === task.id) (el as HTMLInputElement).focus()
                    }
                  "
                  v-model="task.title"
                  type="text"
                  class="w-full text-sm text-gray-200 font-medium mb-2 leading-snug bg-transparent border-none outline-none focus:ring-0"
                  @blur="editingTaskId = null"
                  @keydown.enter="editingTaskId = null"
                  @keydown.escape="editingTaskId = null"
                />
                <h3
                  v-else
                  class="text-sm text-gray-200 font-medium mb-2 leading-snug cursor-text"
                  @click="editingTaskId = task.id"
                >
                  {{ task.title }}
                </h3>

                <!-- Footer -->
                <div class="flex items-center justify-between">
                  <span class="text-xs text-gray-500">{{ task.date }}</span>
                  <div class="flex items-center gap-1">
                    <button
                      class="p-1 opacity-0 group-hover:opacity-100 hover:bg-red-500/20 rounded text-gray-500 hover:text-red-400 transition-opacity"
                      @click="deleteTask(task.id)"
                    >
                      <svg
                        class="w-3.5 h-3.5"
                        fill="none"
                        stroke="currentColor"
                        viewBox="0 0 24 24"
                      >
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
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { computed, h, nextTick, onMounted, onUnmounted, ref, watch, type Component } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

interface Task {
  id: string
  title: string
  columnId: string
  tags: string[]
  date: string
  avatar: string | null
}

interface Command {
  id: string
  label: string
  icon: Component
  shortcut: string[] | null
}

const showCommandMenu = ref(false)
const commandSearch = ref('')
const commandInput = ref<HTMLInputElement | null>(null)
const selectedGroup = ref(0)
const selectedCommand = ref(0)
const sidebarOpen = ref(localStorage.getItem('notta-sidebar-open') !== 'false')
const editingTaskId = ref<string | null>(null)

watch(sidebarOpen, (isOpen) => {
  localStorage.setItem('notta-sidebar-open', String(isOpen))
})

// Icon components as render functions
const KanbanIcon = {
  render: () =>
    h('svg', { class: 'w-4 h-4', fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
      h('path', {
        'stroke-linecap': 'round',
        'stroke-linejoin': 'round',
        'stroke-width': '2',
        d: 'M9 17V7m0 10a2 2 0 01-2 2H5a2 2 0 01-2-2V7a2 2 0 012-2h2a2 2 0 012 2m0 10a2 2 0 002 2h2a2 2 0 002-2M9 7a2 2 0 012-2h2a2 2 0 012 2m0 10V7m0 10a2 2 0 002 2h2a2 2 0 002-2V7a2 2 0 00-2-2h-2a2 2 0 00-2 2',
      }),
    ]),
}
const NotesIcon = {
  render: () =>
    h('svg', { class: 'w-4 h-4', fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
      h('path', {
        'stroke-linecap': 'round',
        'stroke-linejoin': 'round',
        'stroke-width': '2',
        d: 'M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z',
      }),
    ]),
}
const RemindersIcon = {
  render: () =>
    h('svg', { class: 'w-4 h-4', fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
      h('path', {
        'stroke-linecap': 'round',
        'stroke-linejoin': 'round',
        'stroke-width': '2',
        d: 'M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z',
      }),
    ]),
}

const commandGroups = ref([
  {
    name: 'Navigation',
    commands: [
      { id: 'goto-tasks', label: 'Go to Tasks', icon: KanbanIcon, shortcut: ['G', 'T'] },
      { id: 'goto-notes', label: 'Go to Notes', icon: NotesIcon, shortcut: ['G', 'N'] },
      { id: 'goto-reminders', label: 'Go to Reminders', icon: RemindersIcon, shortcut: ['G', 'R'] },
    ],
  },
])

const filteredCommands = computed(() => {
  const search = commandSearch.value.toLowerCase()
  if (!search) return commandGroups.value

  return commandGroups.value.map((group) => ({
    ...group,
    commands: group.commands.filter((cmd) => cmd.label.toLowerCase().includes(search)),
  }))
})

const isSelected = (groupIndex: number, cmdIndex: number): boolean => {
  return selectedGroup.value === groupIndex && selectedCommand.value === cmdIndex
}

const setSelected = (groupIndex: number, cmdIndex: number): void => {
  selectedGroup.value = groupIndex
  selectedCommand.value = cmdIndex
}

const navigateDown = (): void => {
  const groups = filteredCommands.value.filter((g) => g.commands.length > 0)
  if (groups.length === 0) return

  const currentGroupIdx = groups.findIndex((g) => {
    const origIdx = filteredCommands.value.indexOf(g)
    return origIdx === selectedGroup.value
  })

  if (currentGroupIdx === -1) {
    const firstGroup = filteredCommands.value.findIndex((g) => g.commands.length > 0)
    selectedGroup.value = firstGroup
    selectedCommand.value = 0
    return
  }

  const currentGroup = groups[currentGroupIdx]
  if (currentGroup && selectedCommand.value < currentGroup.commands.length - 1) {
    selectedCommand.value++
  } else if (currentGroupIdx < groups.length - 1) {
    const nextGroup = groups[currentGroupIdx + 1]
    if (nextGroup) {
      selectedGroup.value = filteredCommands.value.indexOf(nextGroup)
      selectedCommand.value = 0
    }
  }
}

const navigateUp = (): void => {
  const groups = filteredCommands.value.filter((g) => g.commands.length > 0)
  if (groups.length === 0) return

  const currentGroupIdx = groups.findIndex((g) => {
    const origIdx = filteredCommands.value.indexOf(g)
    return origIdx === selectedGroup.value
  })

  if (currentGroupIdx === -1) return

  if (selectedCommand.value > 0) {
    selectedCommand.value--
  } else if (currentGroupIdx > 0) {
    const prevGroup = groups[currentGroupIdx - 1]
    if (prevGroup) {
      selectedGroup.value = filteredCommands.value.indexOf(prevGroup)
      selectedCommand.value = prevGroup.commands.length - 1
    }
  }
}

const executeCommand = (command?: Command): void => {
  let cmd = command
  if (!cmd || !cmd.id) {
    const group = filteredCommands.value[selectedGroup.value]
    if (group && group.commands[selectedCommand.value]) {
      cmd = group.commands[selectedCommand.value]
    }
  }

  if (cmd) {
    // Handle command execution
    if (cmd.id === 'goto-tasks') {
      router.push('/')
    } else if (cmd.id === 'goto-notes') {
      router.push('/notes')
    } else if (cmd.id === 'goto-reminders') {
      router.push('/reminders')
    }
    showCommandMenu.value = false
    commandSearch.value = ''
  }
}

let lastKeyTime = 0
let lastKey = ''

const handleKeydown = (e: KeyboardEvent): void => {
  const now = Date.now()

  // G + key shortcuts (must press within 500ms)
  if (lastKey === 'g' && now - lastKeyTime < 2500) {
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
    if (showCommandMenu.value) {
      nextTick(() => {
        commandInput.value?.focus()
      })
    }
  }
  if ((e.ctrlKey || e.metaKey) && e.key === 'n') {
    e.preventDefault()
    const firstColumn = columns.value[0]
    if (firstColumn) {
      addTask(firstColumn.id)
    }
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
})

const columns = ref([
  { id: 'todo', name: 'Todo', glowColor: '#6366f1' },
  { id: 'in-progress', name: 'In Progress', glowColor: '#22c55e' },
  { id: 'completed', name: 'Completed', glowColor: '#10b981' },
  { id: 'paused', name: 'Paused', glowColor: '#eab308' },
])

const defaultTasks: Task[] = [
  {
    id: 'KB-524',
    title: 'Implement Search bar with auto-complete',
    columnId: 'in-progress',
    tags: ['Performance', 'Sidebar'],
    date: 'Apr 02',
    avatar: 'linear-gradient(135deg, #f97316, #eab308)',
  },
  {
    id: 'KB-520',
    title: 'Enhance Loading indicator performance',
    columnId: 'in-progress',
    tags: ['Testing', 'Navigation'],
    date: 'Mar 29',
    avatar: 'linear-gradient(135deg, #22c55e, #10b981)',
  },
  {
    id: 'KB-508',
    title: 'Update Modal animations',
    columnId: 'in-progress',
    tags: ['Testing', 'Dropdown'],
    date: 'Mar 17',
    avatar: 'linear-gradient(135deg, #f97316, #ef4444)',
  },
  {
    id: 'KB-525',
    title: 'Update Alert system for critical notifications',
    columnId: 'todo',
    tags: ['Testing', 'Sidebar'],
    date: 'Apr 03',
    avatar: null,
  },
  {
    id: 'KB-513',
    title: 'Refactor Accordion for smoother transitions',
    columnId: 'todo',
    tags: ['Documentation', 'Core Components'],
    date: 'Mar 22',
    avatar: null,
  },
  {
    id: 'KB-505',
    title: 'Improve Tooltip interactivity',
    columnId: 'todo',
    tags: ['Accessibility', 'Cards'],
    date: 'Mar 14',
    avatar: 'linear-gradient(135deg, #8b5cf6, #ec4899)',
  },
  {
    id: 'KB-518',
    title: 'Design new Icon set for better scalability',
    columnId: 'completed',
    tags: ['Performance', 'Theming'],
    date: 'Mar 27',
    avatar: 'linear-gradient(135deg, #22c55e, #3b82f6)',
  },
  {
    id: 'KB-514',
    title: 'Implement Carousel with lazy loading',
    columnId: 'completed',
    tags: ['Design', 'Core Components'],
    date: 'Mar 23',
    avatar: 'linear-gradient(135deg, #ec4899, #f97316)',
  },
  {
    id: 'KB-506',
    title: 'Redesign Dropdown for mobile devices',
    columnId: 'completed',
    tags: ['Testing', 'Tooltip'],
    date: 'Mar 15',
    avatar: 'linear-gradient(135deg, #f97316, #eab308)',
  },
  {
    id: 'KB-511',
    title: 'Integrate new Select component behavior',
    columnId: 'paused',
    tags: ['Refactor', 'Data Tables'],
    date: 'Mar 20',
    avatar: null,
  },
  {
    id: 'KB-507',
    title: 'Fix Form validation issues',
    columnId: 'paused',
    tags: ['Internationalization', 'Tooltip'],
    date: 'Mar 16',
    avatar: null,
  },
  {
    id: 'KB-503',
    title: 'Refactor Sidebar for better accessibility',
    columnId: 'paused',
    tags: ['Design', 'Sidebar'],
    date: 'Mar 12',
    avatar: 'linear-gradient(135deg, #3b82f6, #8b5cf6)',
  },
]

const loadTasks = (): Task[] => {
  const saved = localStorage.getItem('notta-tasks')
  if (saved) {
    try {
      return JSON.parse(saved)
    } catch {
      return defaultTasks
    }
  }
  return defaultTasks
}

const tasks = ref<Task[]>(loadTasks())

watch(
  tasks,
  (newTasks) => {
    localStorage.setItem('notta-tasks', JSON.stringify(newTasks))
  },
  { deep: true },
)

let draggedTask: Task | null = null
const dragOverTaskId = ref<string | null>(null)

const getColumnTasks = (columnId: string): Task[] => {
  return tasks.value.filter((task) => task.columnId === columnId)
}

const handleDragStart = (task: Task): void => {
  draggedTask = task
}

const handleDragOver = (task: Task): void => {
  if (draggedTask && draggedTask.id !== task.id) {
    dragOverTaskId.value = task.id
  }
}

const handleDragLeave = (): void => {
  dragOverTaskId.value = null
}

const handleTaskDrop = (targetTask: Task): void => {
  if (draggedTask && draggedTask.id !== targetTask.id) {
    const draggedIndex = tasks.value.findIndex((t) => t.id === draggedTask!.id)
    const targetIndex = tasks.value.findIndex((t) => t.id === targetTask.id)

    if (draggedIndex !== -1 && targetIndex !== -1) {
      // Remove dragged task
      const removed = tasks.value.splice(draggedIndex, 1)[0]
      if (removed) {
        // Update column if different
        removed.columnId = targetTask.columnId
        // Insert at target position
        const newTargetIndex = tasks.value.findIndex((t) => t.id === targetTask.id)
        tasks.value.splice(newTargetIndex, 0, removed)
      }
    }
  }
  draggedTask = null
  dragOverTaskId.value = null
}

const handleDrop = (columnId: string): void => {
  if (draggedTask) {
    draggedTask.columnId = columnId
    draggedTask = null
  }
  dragOverTaskId.value = null
}

const addTask = (columnId: string): void => {
  const newId = `KB-${Math.floor(Math.random() * 900) + 100}`
  tasks.value.unshift({
    id: newId,
    title: '',
    columnId,
    tags: ['New'],
    date: new Date().toLocaleDateString('en-US', { month: 'short', day: '2-digit' }),
    avatar: `linear-gradient(135deg, hsl(${Math.random() * 360}, 70%, 50%), hsl(${Math.random() * 360}, 70%, 50%))`,
  })
  nextTick(() => {
    editingTaskId.value = newId
  })
}

const deleteTask = (taskId: string): void => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId)
}
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.05s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
