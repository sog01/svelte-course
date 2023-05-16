<svelte:options immutable={true} />

<script>
	import TodoList from './lib/TodoList.svelte'
	import { v4 as uuid } from 'uuid'
	import { tick, onMount } from 'svelte'

	let todoList
	let showTodoList = true

	let todos = null
	let error = null
	let isLoading = false
	let isAdding = false

	onMount(() => {
		loadTodos()
	})

	async function loadTodos() {
		isLoading = true
		await fetch('https://jsonplaceholder.typicode.com/todos?_limit=10').then(
			async (response) => {
				if (response.ok) {
					todos = await response.json()
				} else {
					error = 'An error has occurred'
				}
			}
		)
		isLoading = false
	}

	async function handleAddTodo(event) {
		event.preventDefault()
		isAdding = true
		await fetch('https://jsonplaceholder.typicode.com/todos', {
			method: 'POST',
			body: JSON.stringify({
				title: event.detail.title,
				completed: false
			}),
			headers: {
				'Content-type': 'application/json;charset=UTF-8'
			}
		}).then(async (response) => {
			if (response.ok) {
				const todo = await response.json()
				todos = [...todos, { ...todo, id: uuid() }]
				todoList.clearInput()
			} else {
				alert('An error has occurred.')
			}
		})
		isAdding = false
		await tick()
		todoList.focusInput()
	}

	function handleRemoveTodo(event) {
		todos = todos.filter((t) => t.id !== event.detail.id)
	}

	function handleToggleTodo(event) {
		todos = todos.map((t) => {
			if (t.id === event.detail.id) {
				return { ...t, completed: event.detail.value }
			}
			return { ...t }
		})
	}
</script>

<input type="checkbox" bind:checked={showTodoList} />
<label for="">Show / Hide list</label>

{#if showTodoList}
	<div style:max-width="500px">
		<TodoList
			{todos}
			{error}
			{isLoading}
			disableAdding={isAdding}
			bind:this={todoList}
			on:addtodo={handleAddTodo}
			on:removetodo={handleRemoveTodo}
			on:toggletodo={handleToggleTodo}
		/>
	</div>
{/if}
