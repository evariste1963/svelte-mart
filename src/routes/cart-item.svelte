<script lang="ts">
	import type { CartProduct } from '$lib/types';
	import Minus from 'phosphor-svelte/lib/Minus';
	import Plus from 'phosphor-svelte/lib/Plus';
	import Trash from 'phosphor-svelte/lib/Trash';

	type Props = {
		cartProduct: CartProduct;
		removeItem: (id: number) => void;
	};

	let { cartProduct = $bindable(), removeItem }: Props = $props();
</script>

{#if cartProduct.quantity > 0}
	<div class="flex items-center justify-between border-b border-gray-200 py-2">
		<div class="flex items-center">
			<img
				src={cartProduct.product.thumbnail}
				alt="Product"
				class="mr-4 size-16 rounded object-cover"
			/>
			<div >
				<p class="text-2xl">{cartProduct.product.title}</p>
				<p class="text-2xl">£{cartProduct.product.price} each</p>
			</div>
		</div>
		<div class="flex items-center">
			<button
				onclick={() =>
					cartProduct.quantity > 1 ? cartProduct.quantity-- : removeItem(cartProduct.id)}
				class="rounded p-1 hover:bg-gray-200"
				aria-label="Subtract 1 from quantity"
			>
				<Minus class="size-5" />
			</button>
			<span class="mx-2 text-2xl">{cartProduct.quantity}</span>
			<button
				onclick={() => cartProduct.quantity++}
				class="rounded p-1 hover:bg-gray-200"
				aria-label="Add 1 to quantity"
			>
				<Plus class="size-5" />
			</button>
			<button
				onclick={() => removeItem(cartProduct.id)}
				class="ml-4 rounded p-1 text-red-500 hover:bg-red-100"
			>
				<Trash class="size-8" />
			</button>
		</div>
	</div>
{/if}
