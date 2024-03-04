<script lang="ts">
	import type { List } from '$lib/types';
	import { Input } from '$lib/components/ui/input';
	import { Button } from '$lib/components/ui/button';

	let topLeft = $state('');
	let topRight = $state('');
	let bottomLeft = $state('');

	const bottomRight = $derived(doTheMath());

	let title = $state('');
	let list = $state<List[]>([]);

	$effect(() => {
		const savedList = localStorage.getItem('list');
		savedList && (list = JSON.parse(savedList));
	});

	$effect(() => {
		localStorage.setItem('list', JSON.stringify(list));
	});

	function doTheMath() {
		const result = (Number(bottomLeft) * Number(topRight)) / Number(topLeft);

		if (Number.isNaN(result)) return '0';

		return result.toFixed(4).replace('.0000', '').replace('.', ',');
	}

	function saveOperation() {
		if (!title || list.some((item) => item.title === title)) return;

		list = [...list, { topLeft, topRight, bottomLeft, title }];
	}

	function useOperation(item: List) {
		topLeft = item.topLeft;
		topRight = item.topRight;
		bottomLeft = item.bottomLeft;
		title = item.title;
	}

	function removeOperation(item: List) {
		list = list.filter((i) => i.title !== item.title);
	}
</script>

<section class="flex flex-col justify-around gap-4">
	<div class="flex gap-4">
		<Input class="basis-1/2" type="number" bind:value={topLeft} />
		<Input class="basis-1/2" type="number" bind:value={topRight} />
	</div>
	<div class="flex gap-4">
		<Input class="basis-1/2" type="number" bind:value={bottomLeft} />
		<Input class="basis-1/2" type="number" value={bottomRight} disabled />
	</div>
</section>

<div class="mt-4 flex w-full items-center space-x-2">
	<Input type="text" bind:value={title} />
	<Button on:click={saveOperation}>Salvar Operação</Button>
</div>

<section class="mt-8 flex max-h-[30%] flex-col gap-2 overflow-scroll">
	{#each list as item}
		<div class="flex items-center justify-between">
			<Button
				class="material-icons text-xl"
				variant="destructive"
				on:click={() => removeOperation(item)}>delete_forever</Button
			>
			{item.title}
			<Button class="material-icons text-xl" on:click={() => useOperation(item)}>check</Button>
		</div>
	{/each}
</section>
