<script lang="ts">
	import type { Terminal, Variable } from "$lib/stores/grammar";
	import { createEventDispatcher } from "svelte";

	export let symbol: Variable | Terminal | null = null;
	export let tabindex: number = -1;

	let dispatcher = createEventDispatcher();
	function showPossibilities(symbol: Variable): undefined {
		dispatcher("nonterminal", symbol);
	}

	function keyPossibilities(e: KeyboardEvent, symbol: Variable | Terminal): undefined {
		if (e.key === "Enter") {
			dispatcher("nonterminal", symbol as Variable);
		}
	}
</script>

{#if symbol === null}
	<span class="terminal">ε</span>
{:else if typeof symbol === "string"}
	{#if symbol === " "}
		<span class="terminal">␣</span>
	{:else}
		<span class="terminal">{symbol}</span>
	{/if}
{:else}
	<span
		class="nonterminal"
		on:keypress={(e) => keyPossibilities(e, symbol)}
		on:click={showPossibilities(symbol)}
		{tabindex}
		role="button"
	>
		{symbol.letter}
	</span>
{/if}

<style lang="scss">
	.nonterminal {
		background-color: lightblue;
		border-radius: 25%;
		padding: 0.5rem;
		margin: 0.5rem;
		cursor: pointer;
	}
</style>
