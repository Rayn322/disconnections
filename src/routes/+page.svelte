<script lang="ts">
	import { SvelteSet } from 'svelte/reactivity';

	const categories = [
		{
			name: 'Category 1',
			items: ['1a', '1b', '1c', '1d']
		},
		{
			name: 'Category 2',
			items: ['2a', '2b', '2c', '2d']
		},
		{
			name: 'Category 3',
			items: ['3a', '3b', '3c', '3d']
		},
		{
			name: 'Category 4',
			items: ['4a', '4b', '4c', '4d']
		}
	];

	const selected = new SvelteSet<string>();
	const foundCategories = new SvelteSet<string>();

	function toggle(item: string) {
		if (selected.has(item)) {
			selected.delete(item);
		} else if (selected.size < 4) {
			selected.add(item);
		}
	}

	function submit() {
		if (selected.size < 4) {
			return;
		}

		const counters = new Map<string, number>();

		selected.forEach((item) => {
			const attemptedCategory = categories.find((category) => category.items.includes(item));

			if (attemptedCategory) {
				if (counters.has(attemptedCategory.name)) {
					// assert not null because we just checked
					const currentCount = counters.get(attemptedCategory.name)!!;
					counters.set(attemptedCategory.name, currentCount + 1);
				} else {
					counters.set(attemptedCategory.name, 1);
				}
			}
		});

		counters.forEach((count, category) => {
			if (count === 4) {
				foundCategories.add(category);
			}
		});

		selected.clear();
	}
</script>

<div class="grid size-96 grid-cols-4 gap-4">
	{#each categories as category}
		{#if foundCategories.has(category.name)}
			<div class="col-span-4 flex w-full items-center justify-center border">
				{category.name}
			</div>
		{/if}
	{/each}

	{#each categories as category}
		{#if !foundCategories.has(category.name)}
			{#each category.items as item}
				<button class="border" class:bg-red-500={selected.has(item)} onclick={() => toggle(item)}>
					{item}
				</button>
			{/each}
		{/if}
	{/each}
</div>

<button class="rounded bg-emerald-500 px-4 py-2" onclick={submit}>Submit</button>
