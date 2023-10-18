<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const tasks = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const tasks_asc = computed(() => tasks.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(tasks, (newVal) => {
	localStorage.setItem('tasks', JSON.stringify(newVal))
}, {
	deep: true
})

const addtask = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	tasks.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removetask = (task) => {
	tasks.value = tasks.value.filter((t) => t !== task)
}
const completedtask = (task) => {
	if (tasks.value.category ==='completed'){
		return
	}
}


onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				ADD And Remove TASK
			</h2>
		</section>

		<section class="create-task">
			<h3>CREATE A Task</h3>

			<form id="new-task-form" @submit.prevent="addtask">
				<h4>ADD TASK HERE</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Task00"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="complete"
							v-model="input_category" />
						<span class="bubble complete"></span>
						<div>complete</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="incomplete"
							v-model="input_category" />
						<span class="bubble incomplete"></span>
						<div>incomplete</div>
					</label>

				</div>

				<input type="submit" value="Add task" />
			</form>
		</section>

		<section class="task-list">

			<h3>TASK LIST</h3>
			<div class="list" id="task-list">

				<div v-for="task in tasks_asc" :class="`task-item ${task.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="task.done" />
						<span :class="`bubble ${
							task.category == 'complete' 
								? 'complete' 
								: 'incomplete'
						}`"></span>
					</label>

					<div class="task-content">
						<input type="text" v-model="task.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removetask(task)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
