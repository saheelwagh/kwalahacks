<script>
	import { onMount } from 'svelte';
	import { ethers } from 'ethers';

	const contractAddress = '0xd9145CCE52D386f254917e481eB44e9943F39138';
	const contractABI = [
		{
			"inputs": [
				{
					"internalType": "address",
					"name": "to",
					"type": "address"
				},
				{
					"internalType": "string",
					"name": "uri",
					"type": "string"
				}
			],
			"name": "mint",
			"outputs": [],
			"stateMutability": "nonpayable",
			"type": "function"
		},
		{
			"inputs": [
				{
					"internalType": "uint256",
					"name": "tokenId",
					"type": "uint256"
				}
			],
			"name": "tokenURI",
			"outputs": [
				{
					"internalType": "string",
					"name": "",
					"type": "string"
				}
			],
			"stateMutability": "view",
			"type": "function"
		}
	];

	let provider;
	let signer;
	let contract;
	let account = '';
	let status = '';
	let mintToAddress = '';
	let mintTokenURI = '';
	let queryTokenId = '';
	let queriedTokenURI = '';

	onMount(() => {
		if (typeof window.ethereum === 'undefined') {
			status = 'Please install MetaMask!';
		}
	});

	async function connectWallet() {
		if (typeof window.ethereum !== 'undefined') {
			try {
				provider = new ethers.BrowserProvider(window.ethereum);
				await provider.send('eth_requestAccounts', []);
				signer = await provider.getSigner();
				account = await signer.getAddress();
				contract = new ethers.Contract(contractAddress, contractABI, signer);
				status = `Connected: ${account}`;
			} catch (error) {
				console.error('Failed to connect wallet:', error);
				status = 'Failed to connect wallet.';
			}
		} else {
			status = 'Please install MetaMask!';
		}
	}

	async function mintNFT() {
		if (!contract || !mintToAddress || !mintTokenURI) {
			status = 'Please connect wallet and fill in all minting fields.';
			return;
		}
		try {
			status = 'Minting NFT...';
			const tx = await contract.mint(mintToAddress, mintTokenURI);
			await tx.wait();
			status = 'NFT minted successfully!';
		} catch (error) {
			console.error('Failed to mint NFT:', error);
			status = 'Failed to mint NFT.';
		}
	}

	async function getTokenURI() {
		if (!contract || !queryTokenId) {
			status = 'Please connect wallet and provide a Token ID.';
			return;
		}
		try {
			status = 'Fetching Token URI...';
			queriedTokenURI = await contract.tokenURI(queryTokenId);
			status = 'Token URI fetched.';
		} catch (error) {
			console.error('Failed to get Token URI:', error);
			status = 'Failed to get Token URI.';
			queriedTokenURI = '';
		}
	}
</script>

<svelte:head>
	<title>NFT Minter</title>
</svelte:head>

<div class="container">
	<h1>NFT Minter</h1>

	<button on:click={connectWallet}>Connect Wallet</button>
	{#if status}<p><strong>Status:</strong> {status}</p>{/if}

	<div class="card">
		<h2>Mint New NFT</h2>
		<p>Only the contract owner can mint.</p>
		<div class="form-group">
			<label for="mint-to">Recipient Address:</label>
			<input id="mint-to" type="text" bind:value={mintToAddress} placeholder="0x..." />
		</div>
		<div class="form-group">
			<label for="mint-uri">Token URI:</label>
			<input id="mint-uri" type="text" bind:value={mintTokenURI} placeholder="ipfs://... or https://..." />
		</div>
		<button on:click={mintNFT}>Mint NFT</button>
	</div>

	<div class="card">
		<h2>Get Token URI</h2>
		<div class="form-group">
			<label for="query-id">Token ID:</label>
			<input id="query-id" type="number" bind:value={queryTokenId} placeholder="0" />
		</div>
		<button on:click={getTokenURI}>Get Token URI</button>
		{#if queriedTokenURI}<p><strong>Token URI:</strong> {queriedTokenURI}</p>{/if}
	</div>
</div>

<style>
	:global(body) {
		background-color: #f7f7f7;
		color: #333;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
	}

	.container {
		max-width: 600px;
		margin: 2rem auto;
		padding: 1rem;
		text-align: center;
	}

	h1 {
		color: #E8831D;
		font-weight: 600;
	}

	.card {
		background-color: #fff;
		border: 1px solid #e0e0e0;
		border-radius: 12px;
		padding: 1.5rem;
		margin-top: 1.5rem;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
		text-align: left;
	}

	h2 {
		margin-top: 0;
		color: #333;
	}

	.form-group {
		display: flex;
		flex-direction: column;
		align-items: flex-start;
		margin-bottom: 1rem;
		width: 100%;
	}

	label {
		margin-bottom: 0.5rem;
		font-weight: 500;
	}

	input {
		width: 100%;
		padding: 0.75rem;
		box-sizing: border-box;
		border: 1px solid #ccc;
		border-radius: 8px;
		transition: border-color 0.2s;
	}

	input:focus {
		border-color: #E8831D;
		outline: none;
	}

	button {
		background-color: #E8831D;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 8px;
		font-weight: 600;
		cursor: pointer;
		transition: background-color 0.2s;
		margin-top: 1rem;
		width: 100%;
	}

	button:hover {
		background-color: #d9740d;
	}

	p strong {
		color: #E8831D;
	}
</style>
