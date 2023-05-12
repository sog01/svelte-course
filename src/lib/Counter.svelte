<script>
	export let initialCount = 0
	export let maxCount = undefined

	let count = initialCount

	$: string = `You have clicked ${count} times.`

	function increment() {
		if (count === maxCount) return
		count++
	}
</script>

<button on:click={increment}>
	Clicks {count}
</button>

<h3>{string}</h3>

<style>
	button {
		padding: 15px 20px;
	}
</style>
