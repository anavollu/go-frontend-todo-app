<script lang="ts">
	import { onMount } from 'svelte';

	let todos: { ID: number; name: string; done: boolean }[] = [];
	let formData: { name: string; done: boolean } = { name: '', done: false };

	onMount(async () => {
		const response = await fetch('http://localhost:3000/todo?limit=50');
		const data = await response.json();
		todos = data.todos;
	});

	async function createTodo() {
		let data;
		let dataList = [];
		const headersList = {
			'Content-Type': 'application/json'
		};

		const bodyContent = JSON.stringify(formData);

		const response = await fetch('http://localhost:3000/todo', {
			method: 'POST',
			body: bodyContent,
			headers: headersList
		});

		if (response.ok) {
			data = await response.text();
			const updatedResponse = await fetch('http://localhost:3000/todo?limit=50');
			dataList = await updatedResponse.json();
			todos = dataList.todos;
		} else {
			console.error(`Error creating data.`);
		}
		console.log(data);
	}

	async function deleteTodo(id: number) {
		let data;
		let dataList = [];

		let headersList = {
			'Content-Type': 'application/json'
		};

		let response = await fetch(`http://localhost:3000/todo/${id}`, {
			method: 'DELETE',
			headers: headersList
		});

		if (response.ok) {
			data = await response.text();
			const updatedResponse = await fetch('http://localhost:3000/todo?limit=50');
			dataList = await updatedResponse.json();
			todos = dataList.todos;
		} else {
			console.error(`Error deleting data with ID ${id}.`);
		}
	}

	async function updateTodo(id: number) {
		let data;
		let headersList = {
			'Content-Type': 'application/json'
		};

		let bodyContent = JSON.stringify(formData);

		let response = await fetch(`http://localhost:3000/todo/${id}`, {
			method: 'PUT',
			body: bodyContent,
			headers: headersList
		});

		if (response.ok) {
			data = await response.text();
		} else {
			console.error(`Error updating data with ID ${id}.`);
		}
		console.log(data);
	}
</script>

<main class="max-w-2xl flex flex-col m-auto">
	<h1 class="font-bold text-xl text-center">Svelte & Go | To-Do</h1>

	<div class="border border-black p-4">
		<p class="font-semibold mb-2 text-center">To-Do list</p>
		<div class="flex flex-col gap-2">
			{#each todos as todo}
				<div class="grid grid-cols-[3fr_2fr_1fr]">
					<p>Name: {todo.name}</p>
					<p>Done: {todo.done}</p>
					<button
						on:click={() => deleteTodo(todo.ID)}
						class="bg-red-600 text-white rounded-md p-1 text-sm">Delete</button
					>
				</div>
			{/each}
		</div>
	</div>

	<div class="border border-black p-4">
		<p class="font-semibold mb-2 text-center">Create todo</p>
		<form on:submit|preventDefault={createTodo} class="flex flex-col gap-2">
			<div class="flex gap-4 items-center">
				<label for="name">Task name</label>
				<input
					id="name"
					type="text"
					class="border border-black rounded-md px-2"
					bind:value={formData.name}
				/>
			</div>
			<div class="flex gap-4 items-center">
				<label for="done">Done</label>
				<input id="done" type="checkbox" class="w-4 h-4" bind:checked={formData.done} />
			</div>
			<button class="bg-blue-400 rounded-md text-white font-semibold" type="submit">Create</button>
		</form>
	</div>
</main>
