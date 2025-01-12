<script lang="ts">
	import Minus from 'phosphor-svelte/lib/Minus';
	import Plus from 'phosphor-svelte/lib/Plus';
	import Trash from 'phosphor-svelte/lib/Trash';
</script>

<div class="flex items-center justify-between border-b border-gray-200 py-2">
	<div class="flex items-center">
		<img
			src="https://via.placeholder.com/50"
			alt="Product"
			class="mr-4 size-12 rounded object-cover"
		/>
		<div>
			<p class="font-medium">Product Name</p>
			<p class="text-sm">$19.99 each</p>
		</div>
	</div>
	<div class="flex items-center">
		<button class="rounded p-1 hover:bg-gray-200" aria-label="Subtract 1 from quantity">
			<Minus class="size-4" />
		</button>
		<span class="mx-2">2</span>
		<button class="rounded p-1 hover:bg-gray-200" aria-label="Add 1 to quantity">
			<Plus class="size-4" />
		</button>
		<button class="ml-4 rounded p-1 text-red-500 hover:bg-red-100">
			<Trash class="size-4" />
		</button>
	</div>
</div>
