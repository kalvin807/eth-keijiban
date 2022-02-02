<script lang="ts">
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

{#if provider && typeof provider !== 'string'}
	<p>Welcome to ETH kanban</p>
	<BlockHeightDisplay {blockHeight} />
	{#each blockStore.reverse() as n}
		<BlockTxn {provider} blockNumber={n} />
	{/each}
{:else if provider === null}
	<p>No metamask!</p>
{:else}
	<p>Loading!</p>
{/if}
