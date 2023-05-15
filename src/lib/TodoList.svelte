<script>
	import Button from './Button.svelte'
	import { createEventDispatcher, onMount } from 'svelte'

	onMount(() => {
		console.log('mounted')
	})

	export let todos = []
	export const readonly = 'readonly'
	export function clearInput() {
		inputText = ''
	}
	export function focusInput() {
		input.focus()
	}
	let inputText
	let input

	const dispatch = createEventDispatcher()
	function handleAddTodo() {
		const isCancelable = dispatch(
			'addtodo',
			{
				title: inputText
			},
			{
				cancelable: true
			}
		)
		if (isCancelable) {
			inputText = ''
		}
	}

	function handleRemoveTodo(id) {
		dispatch('removetodo', {
			id
		})
	}

	function handleToggleTodo(id, value) {
		dispatch('toggletodo', {
			id,
			value
		})
	}
</script>

<div class="todo-list-wrapper">
	<div class="todo-list">
		<ul>
			{#each todos as { id, title, completed } (id)}
				<li>
					<label>
						<input
							on:input={(event) => {
								event.currentTarget.checked = completed
								handleToggleTodo(id, !completed)
							}}
							type="checkbox"
							checked={completed}
						/>
						{title}
					</label>
					<button on:click={() => handleRemoveTodo(id)}>Remove</button>
				</li>
			{/each}
		</ul>
	</div>
	<form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
		<input bind:this={input} bind:value={inputText} />
		<Button type="submit" disabled={!inputText}>Add</Button>
	</form>
</div>

<style>
	.todo-list {
		max-height: 150px;
		overflow: auto;
	}
</style>
