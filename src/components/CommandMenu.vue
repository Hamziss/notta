<template>
  <Teleport to="body">
    <Transition name="fade">
      <div
        v-if="show"
        class="fixed inset-0 z-50 flex items-start justify-center pt-[20vh]"
        @click="$emit('close')"
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
              @keydown.escape="$emit('close')"
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
</template>

<script setup lang="ts">
import { computed, h, nextTick, ref, watch, type Component } from 'vue'
import { useRouter } from 'vue-router'

interface Command {
  id: string
  label: string
  icon: Component
  shortcut: string[] | null
}

interface CommandGroup {
  name: string
  commands: Command[]
}

const props = defineProps<{
  show: boolean
}>()

const emit = defineEmits<{
  close: []
}>()

const router = useRouter()
const commandSearch = ref('')
const commandInput = ref<HTMLInputElement | null>(null)
const selectedGroup = ref(0)
const selectedCommand = ref(0)

// Focus input when menu opens
watch(
  () => props.show,
  (isOpen) => {
    if (isOpen) {
      commandSearch.value = ''
      selectedGroup.value = 0
      selectedCommand.value = 0
      nextTick(() => {
        commandInput.value?.focus()
      })
    }
  },
)

// Icon components
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

const commandGroups: CommandGroup[] = [
  {
    name: 'Navigation',
    commands: [
      { id: 'goto-tasks', label: 'Go to Tasks', icon: KanbanIcon, shortcut: ['G', 'T'] },
      { id: 'goto-notes', label: 'Go to Notes', icon: NotesIcon, shortcut: ['G', 'N'] },
      { id: 'goto-reminders', label: 'Go to Reminders', icon: RemindersIcon, shortcut: ['G', 'R'] },
    ],
  },
]

const filteredCommands = computed(() => {
  const search = commandSearch.value.toLowerCase()
  if (!search) return commandGroups

  return commandGroups.map((group) => ({
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
    if (cmd.id === 'goto-tasks') {
      router.push('/')
    } else if (cmd.id === 'goto-notes') {
      router.push('/notes')
    } else if (cmd.id === 'goto-reminders') {
      router.push('/reminders')
    }
    emit('close')
  }
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.05s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
