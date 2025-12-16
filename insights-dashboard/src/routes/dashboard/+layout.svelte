<script>
    let { children } = $props();

	import favicon from '$lib/assets/favicon.svg';
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

<header class="bg-white border-b border-gray-200 px-6 py-4">
	<div class="mx-auto max-w-6xl flex items-center justify-between">
		<div>
			<h1 class="text-2xl font-bold text-gray-900">
				<a class="hover:text-blue-600" href="/">GPO Insights</a>
			</h1>
			<p class="text-sm text-gray-600 mt-1">Track your purchasing performance and rebates</p>
		</div>
	</div>
</header>

<main class="flex-1 bg-gray-50">
    {@render children()}
</main>


