<script lang="ts">
	import ScriptSelect from '$lib/ScriptSelect.svelte';
	import { t } from '$lib/convert';
	interface Props {
		children?: import('svelte').Snippet;
	}

	let { children }: Props = $props();
</script>

<header>
	<a class="logo" href="/">
		{$t('Aynuitak-nuye Ukosamaani Sait')}
	</a>

	<nav>
		<!-- <ul> ... </ul> -->
		<ScriptSelect />
	</nav>
</header>
{@render children?.()}

<style>
	.logo {
		font-size: 1.3em;
		font-weight: bold;
		margin: 0;
	}

	header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		width: 100%;
		padding: 1em 1.5em;
		background: var(--color-theme-lighter);
		color: var(--color-theme-text);
		box-shadow: 0 0 0.5em var(--color-theme);
	}

	header a {
		color: inherit;
		text-decoration: none;
		text-shadow: var(--text-shadow-brighten);
	}

	header a:hover {
		text-decoration: underline;
	}

	nav {
		display: contents;
	}
</style>
