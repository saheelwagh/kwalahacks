<script>
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';
	
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<header>
	<nav>
		<a href="/">Home</a>
		<a href="/nft">NFT Minter</a>
	</nav>
</header>

<main>
	{@render children?.()}
</main>

<style>
	header {
		background-color: #ffffff;
		padding: 1rem 2rem;
		border-bottom: 1px solid #e0e0e0;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.04);
	}

	nav {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 2rem;
	}

	nav a {
		text-decoration: none;
		color: #444;
		font-weight: 600;
		font-size: 1.1rem;
		padding: 0.5rem 1rem;
		border-radius: 8px;
		transition: color 0.2s, background-color 0.2s;
	}

	nav a:hover {
		color: #E8831D;
		background-color: #fdf5ec;
	}

	main {
		padding: 2rem;
	}
</style>
