<template>
	<article class="column">
		<header class="flex items-center justify-between w-full">
			<h2 class="text-xl font-medium mb-3">
				{{ formattedDate }}
			</h2>
			<!-- <Plus
				name="add"
				class="column-card-header-icon"
				@click="openAddTaskModal"
			/> -->
		</header>

		<draggable
			:list="tasks"
			group="tasks"
			item-key="id"
			class="flex flex-col gap-4"
			@change="updateTasks"
		>
			<template #item="{ element }">
				<ColumnCard
					:key="element.id"
					:task="element"
					@edit="editTask"
					@delete="deleteTask"
				/>
			</template>
		</draggable>
	</article>
</template>

<script setup lang="ts">


import { format, parseISO } from 'date-fns'
import draggable from 'vuedraggable'
import { Plus } from 'lucide-vue-next'
import ColumnCard from './columnCard.vue'
import { Task } from '@/composables/types'


const props = defineProps({
  date: {
    type: String,
    required: true
  },
  tasks: {
    type: Array as PropType<Task[]>,
    required: true
  }
})

const emit = defineEmits<{(e: 'add-task', date: string, task: Task): void;
  (e: 'update-task', date: string, taskId: string | null, updatedTask:Task[]): void;
  (e: 'delete-task', date: string, taskId: string): void;
}>()

const formattedDate = computed(() => {
  return format(parseISO(props.date), 'EEEE, MMM d')
})

const openAddTaskModal = () => {
  // Implement your modal logic here
  const newTask: Task = {
    id: Math.random().toString(36).substr(2, 9),
    title: 'New Task',
    description: 'Task description',
    priority: 'low',
    isRecurring: false,
    estimatedDuration: '0',
    dueDate: props.date
  }
  emit('add-task', props.date, newTask)
}

const updateTasks = () => {
  emit('update-task', props.date, null, props.tasks)
}

const editTask = (task: Task[]) => {
  // Implement your edit modal logic here
  emit('update-task', props.date, task[0].id, task)
}

const deleteTask = (taskId: string) => {
  emit('delete-task', props.date, taskId)
}
</script>

<style scoped>
.column {
  flex: 0 0 300px;
  padding: 1rem;
  background-color: #f1f5f9;
  border-radius: 0.5rem;
  scroll-snap-align: start;
}


</style>
