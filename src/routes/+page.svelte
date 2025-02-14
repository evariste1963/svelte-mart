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
	<span class="title font-effect-shadow-multiple text-8xl font-bold">SvelteMart</span>
	<div class="relative ml-auto flex items-center">
		<button
			onclick={() => (cartOpen = cartStats.totalQty < 1 ? false : !cartOpen)}
			disabled={cartStats.totalQty == 0 ? true : false}
			class={cartStats.totalQty == 0 && !cartOpen
				? 'cart-empty btn flex items-center rounded-full bg-gray-500 px-4 py-2 w-48 h-16 text-xl text-white hover:bg-gray-600'
				: 'flex items-center rounded-full bg-sky-600 px-4 py-2i w-48 h-16 text-xl text-white hover:bg-sky-700'}
		>
			<ShoppingCart class="mr-6 size-10" />
			<span class="justify-items: end mr-2"
				>
        {#if cartStats.totalQty > 0}
          Items {cartStats.totalQty}<br />£ {cartStats.totalCost.toFixed(2)}
        {/if}
			</span></button
		>
		{#if cartOpen}
			<div
				class="absolute right-0 top-16 z-10 mt-2 w-96 rounded-lg bg-white shadow-xl"
				transition:slide
			>
				<div class="relative p-4">
					<h2 class="mb-4 text-3xl font-semibold">Your Cart</h2>
					<button
						class="absolute right-4 top-4 rounded-full p-1 hover:bg-gray-100"
						aria-label="close cart"
						onclick={() => (cartOpen = false)}
					>
						<X class="size-6" />
					</button>
					<!-- eslint-disable-next-line -->
					{#each cartProducts as _, i}
						<CartItem bind:cartProduct={cartProducts[i]} removeItem={removeFromCart} />
					{/each}
					<div class="mt-4 border-gray-200 pt-4">
						<p class="text-3xl font-semibold">Total: £{cartStats.totalCost.toFixed(2)}</p>
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
				<p class="mb-2 overflow-hidden truncate text-4xl font-medium text-gray-800">
					{product.title}
				</p>
				<div class="flex items-center justify-between">
					<p class="text-4xl font-bold">£{product.price}</p>
					<button
						class={product.cartBtnIsDisabled == false ? "rounded-full bg-sky-600 px-4 py-2 w-36 h-12 text-2xl text-white transition-colors duration-300 hover:bg-sky-700" :
            "btn in-cart rounded-full bg-gray-400 px-4 py-2 w-36 h-12 text-2xl text-align transition-colors"}
						onclick={() => addToCart(product)}
						disabled={product.cartBtnIsDisabled}
						>{product.cartBtnIsDisabled ? '' : 'Add to Cart'}
					</button>
				</div>
			</div>
		</div>
	{/each}
</div>

<style>
 
.title {
  font-family: "Caveat";
  font-weight: 800 ;
}
  .btn {
    display:flex ;
    overflow: hidden;
    text-wrap: wrap ;
    text-align:center;
    position:relative;
  }

  .btn.in-cart {
    justify-content: center;
    justify-items: center;

  }
.btn.cart-empty::after,
  .btn.cart-empty::before {
    content: "Items: 0";
    position: absolute;
    width: 100%;
    font-weight: bold;
    line-height: 3.5;
    transition: all 400ms;
    top: 0;
    left:12px;
  }

  .btn.cart-empty::before{
    left: 0;
  }

  .btn.cart-empty:hover::after{
    top: -65px;
    left:12px;
  }

  .btn.cart-empty::before {
    content: "Cart Empty";
    padding-left: 2rem;
    top: 65px;
    color: #fff;
    background-color: rgba(255,0,0,0.333)
  }

  .btn.cart-empty:hover::before {
    top: 0;
}

.btn.in-cart::after,
  .btn.in-cart::before {
    content: "In Cart";
    position: absolute;
    margin: auto;
    color: #ddd;
    width:100%;
    font-weight: bold;
    line-height: 2;
    transition: all 400ms;
    transition-delay: 500ms;
    text-wrap:wrap; top: 0;
  }

  .btn.in-cart:hover::after{
    top: -48px;
  }

  .btn.in-cart::before {
    content: "In Cart";
    top: 48px;
    color: #fff;
   background-color: rgba(7,7,7,0.3);
}

  .btn.in-cart:hover::before {
    top: 0;
}

</style>
