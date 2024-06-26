<!-- https://svelte.dev/repl/347b37e18b5d4a65bbacfd097536db02?version=4.2.17 -->
<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { currentFile } from '$lib/main';
	interface INode {
		name: string;
		children: INode[];
	}

	export let name = '';
	export let children: INode[] = [];
	export let indent = 1;
	export let path = name;
	let selected = false;
	let open = false;

	const dispatch = createEventDispatcher();

	function clickHandler() {
		// If it's a directory, hide and show children
		if (children.length > 0) {
			open = !open;
			// console.log(`Clicked directory with path: "${path}"`);
		} else {
			dispatch('fileselect', {
				path: path
			});
			// console.log(`Clicked file with path: "${path}"`);
		}
	}

	currentFile.subscribe((p) => {
		if (path === p) {
			selected = true;
		} else {
			selected = false;
		}
	});
</script>

<!-- {#if selected} -->
<button
	on:click={clickHandler}
	style="padding-left: {indent}rem"
	class={selected ? 'selected' : ''}
>
	<span class="container">
		{#if children.length > 0}
			{#if !open}
				<svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px"
					><path d="M504-480 320-664l56-56 240 240-240 240-56-56 184-184Z" /></svg
				>
			{:else}
				<!-- Rotate if it's closed -->
				<svg
					style="transform: rotate(90deg)"
					xmlns="http://www.w3.org/2000/svg"
					height="24px"
					viewBox="0 -960 960 960"
					width="24px"><path d="M504-480 320-664l56-56 240 240-240 240-56-56 184-184Z" /></svg
				>
			{/if}
		{/if}
		{name}
	</span>
</button>

{#if open}
	{#each children as child}
		{#if child.children.length === 0}
			<!-- Treat path like file -->
			<svelte:self on:fileselect {...child} indent={indent + 1.5} path={path + child.name} />
		{:else}
			<!-- Treat path like directory -->
			<svelte:self on:fileselect {...child} indent={indent + 1} path={path + child.name + '/'} />
		{/if}
	{/each}
{/if}

<style>
	button {
		display: flex;
		cursor: pointer;
		user-select: none;
		border: none;
		background: none;
		color: var(--foreground-0);
		font-size: inherit;
		width: 98%;
		border-radius: 5px;
		margin-left: 1%;
		margin-top: 0.4rem;
		margin-bottom: 0.4rem;
		padding-top: 0.4rem;
		padding-bottom: 0.4rem;
		white-space: nowrap;
		text-overflow: ellipsis;
	}

	button * {
		text-overflow: ellipsis;
		vertical-align: middle;
	}

	button:hover {
		background-color: var(--background-3);
	}

	.selected {
		background-color: var(--background-3);
		border-left: 3px solid var(--foreground-5);
	}

	.selected:hover {
		background-color: var(--background-4);
	}

	svg {
		fill: var(--background-4);
	}

	.container {
		display: flex;
		align-items: center;
	}
</style>
