<script lang="ts">
	import { afterUpdate, createEventDispatcher } from "svelte";
	import add from "$lib/assets/icons/add.svg";
	import del from "$lib/assets/icons/del.svg";

	export let name: string;
	export let rules: string[] = [""];
	export let group = "";

	if (rules.length === 0) {
		rules = [""]; // reset
	}

	const cursorPosition = {
		position: 0,
		rule: null as null | HTMLInputElement
	};

	afterUpdate(() => {
		if (cursorPosition.rule !== null) {
			cursorPosition.rule.focus();
			cursorPosition.rule.setSelectionRange(cursorPosition.position, cursorPosition.position);
		}
		cursorPosition.rule = null;
	});

	const dispatch = createEventDispatcher();
	function input(element: EventTarget | null, i: number) {
		if (element == null) return;
		const inputElement = element as HTMLInputElement;
		// replace epsilon with ε
		const currentCursorPos = inputElement.selectionStart;
		rules[i] = inputElement.value.replace(/epsilon/g, "ε");
		if (currentCursorPos !== null && rules[i] !== inputElement.value) {
			console.log("cursor pos", currentCursorPos);
			cursorPosition.position = currentCursorPos - 6;
			cursorPosition.rule = inputElement;
		}

		dispatch("input", rules);
	}

	function addRule() {
		rules = [...rules, ""];
		dispatch("input", rules);
	}

	function removeRule(index: number) {
		if (rules.length === 1) {
			rules = [""]; // reset
		} else {
			rules = rules.filter((_, i) => i !== index);
		}
		dispatch("input", rules);
	}
</script>

<div class="prule">
	<input class="prule__start" type="radio" name="ruleStart" value={name} bind:group />
	<h3 class="prule__name">{name}</h3>
	<ul class="prule__applications">
		{#each rules as _, i}
			<li class="prule__applications__application">
				<input type="text" bind:value={rules[i]} on:input={(e) => input(e.target, i)} />
				<button class="prule__applications__removebutton" on:click={() => removeRule(i)}>
					<img src={del} alt="delete" />
				</button>
			</li>
		{/each}
		<button class="prule__applications__addbutton" on:click={addRule}>
			<img src={add} alt="add" />
		</button>
	</ul>
</div>

<style lang="scss">
	.prule {
		display: flex;
		flex-direction: row;
		align-items: flex-start;
		justify-content: flex;
		flex-wrap: nowrap;
		width: min-content;

		border-left: 1px solid $primary-color;
		border-bottom: 1px solid $primary-color;
		padding: 0.5rem;

		&__name {
			font-size: 1.5rem;
			margin: 0;

			&::after {
				content: "→";
				margin-right: 0.5rem;
			}
		}

		&__start {
			align-self: center;
			cursor: pointer;
			--webkit-appearance: none;
			appearance: none;
			background-color: $primary-text-color;
			margin: 0;

			color: $primary-color;
			border: 2px solid $primary-color;
			border-radius: 50%;
			width: 1.2rem;
			height: 1.2rem;
			display: grid;
			place-items: center;

			transition: background-color 0.2s ease-in-out;

			&:checked {
				background-color: $primary-color;
				&::before {
					content: "";
					display: block;
					width: 0.5rem;
					height: 0.5rem;
					background-color: $primary-text-color;
					border-radius: 50%;
				}
			}

			user-select: none;
			&:focus,
			&:focus-visible {
				outline-style: auto;
			}
		}

		&__applications {
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: flex-start;
			flex-wrap: wrap;
			list-style: none;
			margin: 0;
			padding: 0;

			&__application {
				display: flex;
				flex-direction: row;
				flex-wrap: nowrap;
				align-items: center;

				border-bottom: 1px solid $primary-color;

				input {
					border: none;
					padding: 0;
					margin: 0;
				}

				button {
					cursor: pointer;
					border: none;
					background: none;
					font-size: 1.5rem;
					font-weight: bold;
					margin: 0;
					height: 1.2em;

					img {
						height: 1em;
					}
				}
			}

			&__addbutton {
				cursor: pointer;
				border: none;
				background: none;
				font-size: 1.5rem;
				font-weight: bold;
			}
		}
	}
</style>
