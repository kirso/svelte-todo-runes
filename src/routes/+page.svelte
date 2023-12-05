<script lang="ts">
	type Todo = {
		text: string;
		done: boolean;
	};
	type Filters = 'all' | 'active' | 'completed';
	let todos = $state<Todo[]>([]);
	let filter = $state<Filters>('all' as Filters);

	$effect(() => {
		console.log(todos);
	});

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const todoEl = event.target as HTMLInputElement;
		const text = todoEl.value;
		const done = false;
		// const id = window.crypto.randomUUID();
		todos = [...todos, { text, done }];
		todoEl.value = '';
	}

	function editTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement;
		const index = +inputEl.dataset.index!;
		todos[index].text = inputEl.value;
	}

	function toggleTodo(event: Event) {
		const checkboxEl = event.target as HTMLInputElement;
		const index = +checkboxEl.dataset.index!;
		todos[index].done = !todos[index].done;
	}

	function setFilter(newFilter: Filters) {
		filter = newFilter;
	}
</script>

<input on:keydown={addTodo} placeholder="Add todo" type="text" />

<div class="todos">
	{#each todos as todo}
		<div class="todo">
			<input on:input={editTodo} value={todo.text} type="text" />
			<input on:change={toggleTodo} checked={todo.done} type="checkbox" />
		</div>
	{/each}
</div>
<div class="filters">
	{#each ['all', 'active', 'completed'] as filter}
		<button onclick={() => setFilter(filter as Filters)}>{filter}</button>
	{/each}
</div>

<style>
	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}

	.todo {
		position: relative;
	}

	input[type='text'] {
		width: 100%;
		padding: 1rem;
	}

	input[type='checkbox'] {
		top: 50%;
		position: absolute;
		right: 4%;
		translate: 0% -50%;
	}
</style>
