<script>
    let userRole = $state('member');
</script>
<svelte:head>
	<title>GPO Insights Dashboard</title>
</svelte:head>

<div class="min-h-screen bg-gray-50">
	<header class="bg-white shadow">
		<div class="mx-auto max-w-4xl px-4 py-6">
			<div class="flex items-center justify-between">
				<h1 class="text-2xl font-bold text-gray-900"><a href="/dashboard">GPO Insights</a></h1>
				<div class="flex items-center space-x-4">
					<span class="text-sm text-gray-600">View:</span>
					<div class="flex rounded bg-gray-100 p-1">
						<button
							class="rounded px-3 py-1 text-sm {userRole === 'member'
								? 'bg-blue-600 text-white'
								: 'text-gray-700'}"
							onclick={() => userRole = 'member'}
						>
							Member
						</button>
						<button
							class="rounded px-3 py-1 text-sm {userRole === 'executive'
								? 'bg-blue-600 text-white'
								: 'text-gray-700'}"
							onclick={() => userRole = 'executive'}
						>
							Executive
						</button>
					</div>
				</div>
			</div>
		</div>
	</header>

	<main class="mx-auto max-w-4xl px-4 py-12">
		<div class="mb-12 text-center">
			<h2 class="mb-4 text-3xl font-bold text-gray-900">GPO Analytics Dashboard</h2>
			<p class="text-gray-600">
				Track purchasing performance and rebates across your Group Purchasing Organization.
			</p>
		</div>

		<div class="mb-8 grid gap-6 md:grid-cols-2">
			<div class="rounded bg-white p-6 shadow">
				<h3 class="mb-3 text-lg font-semibold">Member Dashboard</h3>
				<p class="mb-4 text-sm text-gray-600">Personal metrics and transaction history.</p>
				<!-- Preload data on hover for faster navigation -->
				<a
					href="/dashboard"
					data-sveltekit-preload-data="hover"
					class="block rounded bg-green-600 px-4 py-2 text-center text-white hover:bg-green-700"
				>
					Access Dashboard
				</a>
			</div>

			<div class="rounded bg-white p-6 shadow">
				<h3 class="mb-3 text-lg font-semibold">Executive Dashboard</h3>
				<p class="mb-4 text-sm text-gray-600">Organization-wide performance insights.</p>
				<!-- Preload data on hover for faster navigation -->
				<a
					href="/dashboard"
					data-sveltekit-preload-data="hover"
					class="block rounded bg-blue-600 px-4 py-2 text-center text-white hover:bg-blue-700"
				>
					Access Dashboard
				</a>
			</div>
		</div>

		<div class="rounded bg-white p-6 text-center shadow">
			<h3 class="mb-4 text-lg font-semibold">Current Stats</h3>
			<div class="grid grid-cols-3 gap-4 text-sm">
				<div>
					<div class="font-bold text-blue-600">$4.2M</div>
					<div class="text-gray-600">Total Volume</div>
				</div>
				<div>
					<div class="font-bold text-green-600">6.1%</div>
					<div class="text-gray-600">Avg Rebate</div>
				</div>
				<div>
					<div class="font-bold text-purple-600">1,247</div>
					<div class="text-gray-600">Members</div>
				</div>
			</div>
		</div>
	</main>
</div>
