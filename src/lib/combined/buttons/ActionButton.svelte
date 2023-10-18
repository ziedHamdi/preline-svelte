<script>
	import { Button } from 'flowbite-svelte';

	export let type = null, text, url = null, disabled = false;
	const css = `${disabled ? 'disabled from-gray-400 to-gray-100 hover:from-gray-100 hover:to-gray-400' : 'from-blue-600 to-violet-600 hover:from-violet-600 hover:to-blue-600'} inline-flex justify-center items-center gap-x-3 text-center bg-gradient-to-tl border border-transparent text-white text-sm font-medium rounded-md focus:outline-none focus:ring-2 focus:ring-blue-600 focus:ring-offset-2 focus:ring-offset-white py-3 px-4 dark:focus:ring-offset-gray-800`;
</script>

{#if type}
	<Button type={type} class={css} {disabled}>
			{text}
			<svg class='w-3 h-3' width='16' height='16' viewBox='0 0 16 16' fill='none'>
			<path d='M5.27921 2L10.9257 7.64645C11.1209 7.84171 11.1209 8.15829 10.9257 8.35355L5.27921 14'
						stroke='currentColor' stroke-width='2' stroke-linecap='round' />
		</svg>
	</Button>
{:else}
	<a href={url ?? '#'} on:click class={css} {disabled}>
		{text}
		<svg class='w-3 h-3' width='16' height='16' viewBox='0 0 16 16' fill='none'>
			<path d='M5.27921 2L10.9257 7.64645C11.1209 7.84171 11.1209 8.15829 10.9257 8.35355L5.27921 14'
						stroke='currentColor' stroke-width='2' stroke-linecap='round' />
		</svg>
	</a>
{/if}

<style>
	.disabled {
      cursor: not-allowed;
      pointer-events: none;
	}
</style>