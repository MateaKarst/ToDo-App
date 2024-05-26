<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const input_content = ref('')

const todos_asc = computed(() => {
	// Filter out any null or undefined values before sorting
	return todos.value.filter(todo => todo !== null && todo !== undefined)
		.sort((a, b) => a.createdAt - b.createdAt)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

// Function to add a new todo
const addTodo = () => {
	if (input_content.value.trim() === '') return  // Prevent adding empty todos

	todos.value.push({
		content: input_content.value.trim(),
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
	input_content.value = ''  // Clear input after adding
}

// Function to remove a todo
const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

// Initialize data from localStorage on component mount
onMounted(() => {

	try {
		const storedTodos = JSON.parse(localStorage.getItem('todos'))
		if (Array.isArray(storedTodos)) {
			todos.value = storedTodos.filter(todo => todo && typeof todo.createdAt === 'number')
		} else {
			todos.value = []
		}
	} catch (error) {
		todos.value = []
	}
})
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				What's up, <span class="name">Tom</span>
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input type="text" name="content" id="content" placeholder="e.g. make a video"
					v-model="input_content" />
				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">
				<div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>
			</div>
		</section>
	</main>
</template>
