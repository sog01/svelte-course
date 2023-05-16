<svelte:options immutable={true} />

<script>
	import TodoList from './lib/TodoList.svelte'
	import { v4 as uuid } from 'uuid'
	import { tick } from 'svelte'

	let todoList
	let showTodoList = true

	let todos = [
		{
			id: uuid(),
			title: 'Todo 1',
			completed: true
		},
		{
			id: uuid(),
			title: 'Todo 2',
			completed: false
		},
		{
			id: uuid(),
			title: 'Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry',
			completed: true
		}
	]

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
	<div style:max-width="500px">
		<TodoList
			{todos}
			bind:this={todoList}
			on:addtodo={handleAddTodo}
			on:removetodo={handleRemoveTodo}
			on:toggletodo={handleToggleTodo}
		/>
	</div>
{/if}
