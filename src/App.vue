<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const todos = ref([])
const input_content = ref('')
const input_category = ref(null)

//use to arrange the list in order
const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

// The Vue. js Watcher or watch property allows the developers to listen to the component data and run whenever they change a particular property.
// The watcher or watch property is a unique Vue. js feature that lets you keep an eye on one property of the component state and run a function when that property value changes.
watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

//used to add the list with unique id date(createdAt)
const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}
	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

//used for deleting the todo list 
const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

//A component is considered mounted after: All of its synchronous child components have been mounted.
onMounted(() => {
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>


<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				TODO APP
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO LIST</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Add todo list...</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="write a note"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Professional</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

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