<script lang="ts">
	import { run, createBubbler, preventDefault } from 'svelte/legacy';

	const bubble = createBubbler();
	import { createEventDispatcher, tick } from 'svelte';
	import { fly, fade } from 'svelte/transition';

	interface Props {
		suggestions: string[];
		box: HTMLDivElement | undefined;
		rect: DOMRect | undefined;
		textArea: HTMLTextAreaElement | undefined;
		formatPage?: (pages: [cur: number, all: number], items: [cur: number, all: number]) => string;
	}

	let {
		suggestions,
		box = $bindable(),
		rect,
		textArea,
		formatPage = (pages, items) => `Page ${pages[0]} of ${pages[1]} (${items[0]} / ${items[1]})`
	}: Props = $props();

	const dispatch = createEventDispatcher<{
		select: string;
	}>();

	const PAGE_SIZE = 9;

	let focused: boolean = $state(false);
	let shown: boolean = $state(false);

	let pages: string[][] = $state([]);

	let page: number = $state(0); // index of the page of suggestions
	let highlighted: number = $state(0); // index of the highlighted suggestion inside a page

	function initEvents(textarea: HTMLTextAreaElement) {
		textarea.addEventListener('keydown', (e) => {
			if (!shown) return;
			if (['1', '2', '3', '4', '5', '6', '7', '8', '9'].includes(e.key)) {
				e.preventDefault();
				return;
			}
			switch (e.key) {
				case 'Tab':
				case 'Enter':
				case 'PageDown':
				case 'PageUp': {
					e.preventDefault();
					break;
				}
			}
		});
		textarea.addEventListener('keyup', async (e) => {
			if (!shown) return;
			console.log(e.key);
			if (['1', '2', '3', '4', '5', '6', '7', '8', '9'].includes(e.key)) {
				const value = pages[page][Number(e.key) - 1];
				if (!value) return;
				dispatch('select', pages[page][Number(e.key) - 1]);
				await tick();
				shown = false;
				e.preventDefault();
				return;
			}
			switch (e.key) {
				case 'Tab':
				case 'Enter': {
					if (!pages[page][highlighted]) return;

					dispatch('select', pages[page][highlighted]);
					await tick();
					shown = false;
					e.preventDefault();
					break;
				}
				case 'Escape': {
					shown = false;
					e.preventDefault();
					break;
				}
				case 'ArrowDown': {
					if (highlighted + 1 === pages[page].length) {
						page = (page + 1) % pages.length;
					}
					highlighted = highlighted + (1 % pages[page].length);
					e.preventDefault();
					break;
				}
				case 'ArrowUp': {
					if (highlighted === 0) {
						page = (page - 1 + pages.length) % pages.length;
					}
					highlighted = (highlighted - 1 + pages[page].length) % pages[page].length;
					e.preventDefault();
					break;
				}
				case 'PageDown': {
					page = (page + 1) % pages.length;
					e.preventDefault();
					break;
				}
				case 'PageUp': {
					page = (page - 1 + pages.length) % pages.length;
					e.preventDefault();
					break;
				}
			}
		});
		textarea.addEventListener('focus', () => (focused = true));
		textarea.addEventListener('blur', () => (focused = false));
	}

	run(() => {
		shown = focused && suggestions.length > 0;
	});
	run(() => {
		console.log(shown);
	});
	run(() => {
		pages = Array.from({ length: Math.ceil(suggestions.length / PAGE_SIZE) }, (_, i) =>
			suggestions.slice(i * PAGE_SIZE, (i + 1) * PAGE_SIZE)
		);
	});
	run(() => {
		if (textArea) {
			initEvents(textArea);
		}
	});
	run(() => {
		(highlighted = 0), page, suggestions;
	});
	run(() => {
		console.log(highlighted, page, suggestions);
	});
</script>

<!-- <svelte:window on:keyup={(e) => {}} /> -->
{#if rect && shown}
	<div
		in:fly
		out:fade={{ duration: 500 }}
		bind:this={box}
		style:left={`${rect.left}px`}
		style:top={`${rect.bottom}px`}
	>
		<ol>
			{#each pages[page] as suggestion, i}
				<li class:active={highlighted === i}>
					<button
						onmousedown={preventDefault(bubble('mousedown'))}
						onclick={async () => {
							dispatch('select', suggestion);
							await tick();
							shown = false;
						}}
					>
						{suggestion}
					</button>
				</li>
			{/each}
		</ol>
		<p>
			{formatPage([page + 1, pages.length], [pages[page].length, suggestions.length])}
		</p>
	</div>
{/if}

<style>
	div {
		position: absolute;
		z-index: 10005;

		box-shadow:
			0 0 0 1px rgba(0, 0, 0, 0.1),
			0 4px 11px rgba(0, 0, 0, 0.18),
			0 4px 15px rgba(0, 0, 0, 0.15),
			0 9px 46px rgba(0, 0, 0, 0.23);

		display: flex;
		flex-direction: column;
		min-width: 15rem;

		font-size: inherit;

		color: var(--color-theme);

		text-shadow: 0 0 2px var(--color-theme-bright-transparent-25);

		background-color: rgba(255, 255, 255, 0.8);
		backdrop-filter: blur(8px);
	}

	ol {
		padding: 0;
		margin: 0;
	}

	p {
		margin: 0;
		padding: 1em;
		font-size: 0.4em;
		color: rgba(84, 84, 84, 0.9);
		background: #e9e9e9;
	}

	li {
		padding: 0.25em;
		margin: 0;
		position: relative;
		list-style-position: inside;
	}

	li::marker {
		color: #545454;
	}

	li.active::marker {
		color: #e9e9e9;
	}

	li.active {
		background: linear-gradient(
			90deg,
			var(--color-theme-lighter) 0%,
			var(--color-theme-bright) 100%
		);
		text-shadow: 0 0 1em rgba(255, 255, 255, 0.25);

		color: white;
	}

	button {
		background: none;
		border: none;
		font-size: inherit;
		font-family: inherit;
		color: inherit;
		cursor: pointer;
		padding: 0;
		text-shadow: inherit;
	}
</style>
