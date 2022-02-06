<script lang="ts">
	import type { providers } from 'ethers';
	import { onMount } from 'svelte';

	export let provider: providers.Web3Provider;
	export let txnHash: string;

	let txn: providers.TransactionResponse;

	onMount(async () => {
		txn = await provider.getTransaction(txnHash);
	});

	function hexToUtf8(s: string) {
		try {
			return decodeURIComponent(
				s
					.replace('0x', '')
					.replace(/\s+/g, '') // remove spaces
					.replace(/[0-9a-f]{2}/g, '%$&') // add '%' before each 2 characters
			);
		} catch (e) {
			return '';
		}
	}
	$: utf8 = txn ? hexToUtf8(txn.data) : undefined;
</script>

{#if utf8}
	<div>
		<fieldset class="transaction">
			<legend>{txn.hash}</legend>
			{utf8}
			<br />
			<a target="_blank" href={`https://etherscan.io/tx/${txn.hash}`}>etherscan</a>
		</fieldset>
	</div>
{/if}
