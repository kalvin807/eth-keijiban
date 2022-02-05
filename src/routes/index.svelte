<script lang="ts">
	import '../index.css';
	import { ethers } from 'ethers';
	import BlockHeightDisplay from '../libs/BlockHeightDisplay.svelte';
	import { onDestroy, onMount } from 'svelte';
	import BlockTxn from '../libs/BlockTxn.svelte';

	export let provider: ethers.providers.Web3Provider | undefined | null = undefined;
	export let blockStore: number[] = [];
	onMount(() => {
		provider =
			typeof (window as any).ethereum !== 'undefined'
				? new ethers.providers.Web3Provider((window as any).ethereum)
				: null;

		if (provider) {
			provider.on('block', (blockNumber: number) => {
				console.log('Block number:', blockNumber);
				blockStore.push(blockNumber);
				blockStore = blockStore;
			});
		}
	});

	onDestroy(() => {
		console.log('winding down');
		if (provider && typeof provider !== 'string') {
			provider.removeAllListeners();
		}
	});

	$: blockHeight = blockStore[blockStore.length - 1] || 0;
</script>

<div class="terminal-nav">
	<div class="terminal-logo">
		<div class="logo terminal-prompt">仮想掲示板 - ETH</div>
	</div>
</div>

<div class="content">
	{#if provider && typeof provider !== 'string'}
		<BlockHeightDisplay {blockHeight} />

		<div class="terminal-timeline">
			{#each blockStore.reverse() as n}
				<BlockTxn {provider} blockNumber={n} />
			{/each}
		</div>
	{:else if provider === null}
		<h1>No metamask!</h1>
	{:else}
		<h1>Loading!</h1>
	{/if}
</div>

<footer>
	<p>By Kal</p>
	<nav>
		<ul>
			<li><a>Github</a></li>
			<li><a>Twitter</a></li>
		</ul>
	</nav>
</footer>
