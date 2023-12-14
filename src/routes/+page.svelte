<script lang="ts">
	type Todo = {
		text: string;
		done: boolean;
	};
	type Filters = 'all' | 'active' | 'completed';
	let todos = $state<Todo[]>([]);
	let filter = $state<Filters>('all' as Filters);
	let filteredTodos = $derived(filterTodo());

	$effect(() => {
		const savedTodos = localStorage.getItem('todos');
		console.log(savedTodos);
		if (savedTodos) {
			todos = JSON.parse(savedTodos);
		}
	});

	$effect(() => {
		localStorage.setItem('todos', JSON.stringify(todos));
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

	function filterTodo() {
		switch (filter) {
			case 'all':
				return todos;
			case 'active':
				return todos.filter((todo) => !todo.done);
			case 'completed':
				return todos.filter((todo) => todo.done);
		}
	}

	function remaining() {
		return todos.filter((todo) => !todo.done).length;
	}
</script>

<input on:keydown={addTodo} placeholder="Add todo" type="text" />

<div class="todos">
	{#each filteredTodos as todo, i}
		<div class:completed={todo.done} class="todo">
			<input on:input={editTodo} value={todo.text} data-index={i} type="text" />
			<input on:change={toggleTodo} checked={todo.done} data-index={i} type="checkbox" />
		</div>
	{/each}
</div>
<div class="filters">
	{#each ['all', 'active', 'completed'] as filter}
		<button onclick={() => setFilter(filter as Filters)}>{filter}</button>
	{/each}
</div>
<p>{remaining()}: remaining</p>

<style>
	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}

	.todo {
		position: relative;
		transition: opacity 0.3s;
	}

	.completed {
		opacity: 0.4;
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

	.filters {
		margin-block: 1rem;
	}
</style>
