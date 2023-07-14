<script lang="ts">
	import { onMount } from 'svelte';

	let todos: { name: string; done: boolean }[] = [];
	let formData: { name: string; done: boolean } = { name: '', done: false };

	onMount(async () => {
		const response = await fetch('http://localhost:3000/todo?limit=50');
		const data = await response.json();
		todos = data.todos;
	});

	async function createTodo() {
		const headersList = {
			'Content-Type': 'application/json'
		};

		const bodyContent = JSON.stringify(formData);

		const response = await fetch('http://localhost:3000/todo', {
			method: 'POST',
			body: bodyContent,
			headers: headersList
		});

		const data = await response.text();
		console.log(data);
	}
</script>

<main>
	<h1 class="font-bold text-xl text-center">Svelte & Go | To-Do</h1>

	<div class="border border-black p-4">
		<p class="font-semibold mb-2">To-Do list</p>
		{#each todos as todo}
			<p>Name:{todo.name}, done:{todo.done}</p>
		{/each}
	</div>

	<div class="border border-black p-4">
		<form on:submit|preventDefault={createTodo} class="flex flex-col gap-2">
			<div class="flex gap-4 items-center">
				<label for="name">Nome da tarefa</label>
				<input
					id="name"
					type="text"
					class="border border-black rounded-md px-2"
					bind:value={formData.name}
				/>
			</div>
			<div class="flex gap-4 items-center">
				<label for="done">Done</label>
				<input id="done" type="checkbox" class="w-4 h-4" bind:value={formData.done} />
			</div>
			<button class="bg-blue-400 rounded-md text-white font-semibold" type="submit"
				>Criar tarefa</button
			>
		</form>
	</div>
</main>
