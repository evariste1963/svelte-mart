<script lang="ts">
	import type { Product, CartProduct } from '$lib/types';
	import CartItem from './cart-item.svelte';
	import ShoppingCart from 'phosphor-svelte/lib/ShoppingCart';
	import X from 'phosphor-svelte/lib/X';
	import { slide } from 'svelte/transition';

	let { data } = $props();
	let cartOpen = $state(false);
	let shopWindow = $state(data);
	let cartProducts = $state<CartProduct[]>([]);

	const cartStats = $derived.by(() => {
		let totalCost = 0;
		let totalQty = 0;
		for (const product of cartProducts) {
			totalCost += product.product.price * product.quantity;
			totalQty += product.quantity;
		}
		return {
			totalQty,
			totalCost
		};
	});

	// add isDisabled key to each Product product and set to false
	shopWindow.products.forEach((prod) => (prod.cartBtnIsDisabled = false));

	const qualifiesForFreeShipping = $derived(cartStats.totalCost >= 50);

	$effect(() => {
		if (qualifiesForFreeShipping) {
			alert('You have qualified for free shipping!');
		}
	});

	function addToCart(product: Product) {
		product.cartBtnIsDisabled = true;
		for (let item of cartProducts) {
			if (item.product.id === product.id) {
				item.quantity += 1;
				cartProducts = cartProducts;
				return;
			}
		}
		cartProducts = [
			...cartProducts,
			{
				id: product.id,
				quantity: 1,
				product: product
			}
		];
	}

	function removeFromCart(id: number) {
		let removedProduct = shopWindow.products.filter((product) => product.id === id);
		removedProduct[0].cartBtnIsDisabled = false;
		cartProducts = cartProducts.filter((product) => product.id != id);
		if (cartStats.totalQty == 0) {
			cartOpen = false;
		}
	}
</script>

<div class="flex items-center bg-gray-300 p-4">
	<span class="text-6xl font-bold">SvelteMart</span>
	<div class="relative ml-auto flex items-center">
		<button
			onclick={() => (cartOpen = cartStats.totalQty < 1 ? false : !cartOpen)}
			disabled={cartStats.totalQty == 0 ? true : false}
			class={cartStats.totalQty == 0 && cartOpen == false
				? 'flex items-center rounded-full bg-gray-500 px-4 py-2 text-white hover:bg-gray-600'
				: 'flex items-center rounded-full bg-sky-600 px-4 py-2 text-white hover:bg-sky-700'}
		>
			<ShoppingCart class="mr-6 size-10" />
			<span class="justify-items: end mr-2"
				>
        {#if cartStats.totalQty > 0}
          Items {cartStats.totalQty}<br />£ {cartStats.totalCost.toFixed(2)}
          {:else}
          Cart<br /> Empty
{/if}
			</span></button
		>
		{#if cartOpen}
			<div
				class="absolute right-0 top-16 z-10 mt-2 w-80 rounded-lg bg-white shadow-xl"
				transition:slide
			>
				<div class="relative p-4">
					<h2 class="mb-4 text-lg font-semibold">Your Cart</h2>
					<button
						class="absolute right-4 top-4 rounded-full p-1 hover:bg-gray-100"
						aria-label="close cart"
						onclick={() => (cartOpen = false)}
					>
						<X class="size-4" />
					</button>
					<!-- eslint-disable-next-line -->
					{#each cartProducts as _, i}
						<CartItem bind:cartProduct={cartProducts[i]} removeItem={removeFromCart} />
					{/each}
					<div class="mt-4 border-gray-200 pt-4">
						<p class="text-lg font-semibold">Total: £{cartStats.totalCost.toFixed(2)}</p>
					</div>
				</div>
			</div>
		{/if}
	</div>
</div>

<div class="grid grid-cols-1 gap-6 bg-gray-100 p-8 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
	{#each shopWindow.products as product}
		<div class="overflow-hidden rounded-xl bg-white shadow-lg">
			<img src={product.thumbnail} alt={product.title} class="h-48 w-full object-cover" />
			<div class="p-4">
				<p class="mb-2 overflow-hidden truncate text-lg font-medium text-gray-800">
					{product.title}
				</p>
				<div class="flex items-center justify-between">
					<p class="text-xl font-bold">£{product.price}</p>
					<button
						class={product.cartBtnIsDisabled == false ? "rounded-full bg-sky-600 px-4 py-2 text-white transition-colors duration-300 hover:bg-sky-700" :
            "rounded-full bg-gray-500 px-4 py-2 text-white transition-colors duration-300 hover:bg-gray-600"}
						onclick={() => addToCart(product)}
						disabled={product.cartBtnIsDisabled}
						>{product.cartBtnIsDisabled ? 'Added to Cart' : 'Add to Cart'}
					</button>
				</div>
			</div>
		</div>
	{/each}
</div>

<style>
	/* button:disabled { */
		/* background-color: #777; */
    /* cursor: default ; */
	/* } */

	/* .tooltip { */
	/* 	position: relative; */
	/* } */
	/**/
	/* .tooltip::after { */
	/* 	content: 'Your cart is empty!'; */
	/* 	display: none; */
	/* 	position: absolute; */
	/* 	top: 102%; */
	/* 	left: 50%; */
	/* 	transform: translateX(-50%); */
	/* 	background-color: #777; */
	/* 	color: #fff; */
	/* 	padding: 1rem; */
	/* 	border-radius: 1rem; */
	/* 	white-space: nowrap; */
	/* } */
	/**/
	/* .tooltip:hover::after { */
	/* 	display: block; */
	/* } */
</style>
