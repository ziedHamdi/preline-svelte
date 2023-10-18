<script>
	export let preTitle, title, titleColored, description;
</script>

<!-- Hero -->
<div
	class="relative overflow-hidden before:absolute before:top-0 before:left-1/2 before:bg-[url('/images/polygon-bg-element.svg')] before:bg-no-repeat before:bg-top before:bg-cover before:w-full before:h-full before:-z-[1] before:transform before:-translate-x-1/2 dark:before:bg-[url('../svg/component/polygon-dark.svg')]">
	<div class='max-w-[85rem] mx-auto px-4 sm:px-6 lg:px-8 pt-24 pb-10'>
		<!-- Announcement Banner -->
		{#if preTitle}
			<div class='flex justify-center'>
			<span
				class='inline-flex items-center gap-x-2 bg-white border border-gray-200 text-sm text-gray-800 p-1 pl-3 rounded-full transition hover:border-gray-300 dark:bg-gray-800 dark:border-gray-700 dark:hover:border-gray-600 dark:text-gray-200'>
				{preTitle}
				<span
					class='py-2 px-3 inline-flex justify-center items-center gap-x-2 rounded-full bg-gray-200 font-semibold text-sm text-gray-600 dark:bg-gray-700 dark:text-gray-400'>
          <svg class='w-2.5 h-2.5' width='16' height='16' viewBox='0 0 16 16' fill='none'>
            <path d='M5.27921 2L10.9257 7.64645C11.1209 7.84171 11.1209 8.15829 10.9257 8.35355L5.27921 14'
									stroke='currentColor' stroke-width='2' stroke-linecap='round' />
          </svg>
        </span>
			</span>
			</div>
		{/if}
		<!-- End Announcement Banner -->

		<!-- Title -->
		<div class='mt-5 max-w-2xl text-center mx-auto'>
			<h1 class='block font-bold text-gray-800 text-4xl md:text-5xl lg:text-6xl dark:text-gray-200'>
				{title}
				<span class='bg-clip-text bg-gradient-to-tl from-blue-600 to-violet-600 text-transparent'>{titleColored}</span>
			</h1>
		</div>
		<!-- End Title -->

		<div class='mt-5 max-w-3xl text-center mx-auto'>
			<p class='text-lg text-gray-600 dark:text-gray-400'>{@html description}</p>
		</div>

		<!-- Buttons -->
		<slot />
		<!-- End Buttons -->


	</div>
</div>
<!-- End Hero -->