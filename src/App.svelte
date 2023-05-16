<svelte:options immutable={true} />

<script>
	import TodoList from './lib/TodoList.svelte'
	import { v4 as uuid } from 'uuid'
	import { tick } from 'svelte'

	let todoList
	let showTodoList = true

	let todos = []
	let promise = loadTodos()

	function loadTodos() {
		return fetch('https://jsonplaceholder.typicode.com/todos?_limit=10').then((response) => {
			if (response.ok) {
				return response.json()
			} else {
				throw new Error('An error has occured.')
			}
		})
	}

	async function handleAddTodo(event) {
		event.preventDefault()
		todos = [
			...todos,
			{
				id: uuid(),
				title: event.detail.title,
				completed: false
			}
		]

		await tick()
		todoList.clearInput()
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
	{#await promise}
		<p>Loading ...</p>
	{:then todos}
		<div style:max-width="500px">
			<TodoList
				{todos}
				bind:this={todoList}
				on:addtodo={handleAddTodo}
				on:removetodo={handleRemoveTodo}
				on:toggletodo={handleToggleTodo}
			/>
		</div>
	{:catch error}
		<p>{error.message || 'An error has occured'}</p>
	{/await}
	<button
		on:click={() => {
			promise = loadTodos()
		}}>Refresh</button
	>
{/if}
