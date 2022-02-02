<script lang="ts">
	import type { providers } from 'ethers';
	import { onMount } from 'svelte';
	import TxnRow from './TxnRow.svelte';

	export let provider: providers.Web3Provider;
	export let blockNumber: number;

	let block: providers.Block;
	let txns: string[] = [];

	onMount(async () => {
		block = await provider.getBlock(blockNumber);
		txns = block.transactions;
	});
</script>

<h3>{blockNumber}</h3>
{#each txns as h}
	<TxnRow {provider} txnHash={h} />
{/each}
