
<script lang="ts">
	type DashboardView = 'member' | 'executive';
	type TimeRange = 'month' | 'quarter' | 'ytd';

	const memberOptions = [
		{ id: 'm-001', name: 'Acme Hospital' },
		{ id: 'm-002', name: 'Lakeside Clinic' },
		{ id: 'm-003', name: 'North Valley Health' }
	] as const;

	const purchases = [
		{ date: '2025-12-15', vendor: 'Supplier A', amount: 1250.0, rebateEarned: 62.5 },
		{ date: '2025-12-12', vendor: 'Supplier C', amount: 980.0, rebateEarned: 49.0 },
		{ date: '2025-12-10', vendor: 'Supplier B', amount: 2100.0, rebateEarned: 105.0 },
		{ date: '2025-12-07', vendor: 'Supplier A', amount: 640.0, rebateEarned: 32.0 },
		{ date: '2025-12-03', vendor: 'Supplier D', amount: 1875.0, rebateEarned: 93.75 }
	] as const;

	const topSuppliers = [
		{ supplier: 'Supplier A', volume: 1600000, share: 38 },
		{ supplier: 'Supplier B', volume: 940000, share: 22 },
		{ supplier: 'Supplier C', volume: 710000, share: 17 },
		{ supplier: 'Other', volume: 950000, share: 23 }
	] as const;

	let selectedMemberId = $state<(typeof memberOptions)[number]['id']>('m-001');
	let view = $state<DashboardView>('member');
	let timeRange = $state<TimeRange>('ytd');

	const formatUsd = (value: number) =>
		new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(value);
</script>

<svelte:head>
	<title>Dashboard - GPO Insights</title>
</svelte:head>

<div class="p-6 space-y-6">
	<!-- Controls Bar (future: member/org dropdown + auth-driven context) -->
	<div class="bg-white rounded-xl shadow-sm border border-gray-200 p-4 sm:p-5">
		<div class="grid grid-cols-1 gap-4 lg:grid-cols-12 lg:items-start">
			<!-- Member selector -->
			<div class="lg:col-span-5">
				<label
					class="block text-xs font-semibold uppercase tracking-wide text-gray-500 leading-4"
					for="memberSelect"
				>
					Member
				</label>
				<div class="mt-2">
					<select
						id="memberSelect"
						class="w-full rounded-lg border border-gray-300 bg-white px-3 py-2.5 text-sm text-gray-900 shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-600 focus:border-blue-600"
						bind:value={selectedMemberId}
					>
						{#each memberOptions as m}
							<option value={m.id}>{m.name}</option>
						{/each}
					</select>
				</div>
				<p class="mt-2 text-xs text-gray-500 lg:hidden">
					Placeholder: this will become a searchable dropdown and/or be derived from auth.
				</p>
			</div>

			<!-- View toggle -->
			<fieldset class="lg:col-span-4">
				<legend class="block text-xs font-semibold uppercase tracking-wide text-gray-500 leading-4">
					View
				</legend>
				<div class="mt-2 inline-flex w-full rounded-lg bg-gray-100 p-1">
					<button
						type="button"
						onclick={() => (view = 'member')}
						aria-pressed={view === 'member'}
						class={`w-1/2 px-3 py-2 rounded-md text-sm font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-blue-600 ${
							view === 'member' ? 'bg-white text-gray-900 shadow-sm' : 'text-gray-700 hover:text-gray-900'
						}`}
					>
						Member
					</button>
					<button
						type="button"
						onclick={() => (view = 'executive')}
						aria-pressed={view === 'executive'}
						class={`w-1/2 px-3 py-2 rounded-md text-sm font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-blue-600 ${
							view === 'executive'
								? 'bg-white text-gray-900 shadow-sm'
								: 'text-gray-700 hover:text-gray-900'
						}`}
					>
						Executive
					</button>
				</div>
			</fieldset>

			<!-- Range filter -->
			<fieldset class="lg:col-span-3">
				<legend class="block text-xs font-semibold uppercase tracking-wide text-gray-500 leading-4">
					Range
				</legend>
				<div class="mt-2 inline-flex w-full rounded-lg bg-gray-100 p-1">
					<button
						type="button"
						onclick={() => (timeRange = 'month')}
						aria-pressed={timeRange === 'month'}
						class={`w-1/3 px-3 py-2 rounded-md text-sm font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-gray-900 ${
							timeRange === 'month'
								? 'bg-gray-900 text-white shadow-sm'
								: 'text-gray-700 hover:text-gray-900'
						}`}
					>
						Month
					</button>
					<button
						type="button"
						onclick={() => (timeRange = 'quarter')}
						aria-pressed={timeRange === 'quarter'}
						class={`w-1/3 px-3 py-2 rounded-md text-sm font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-gray-900 ${
							timeRange === 'quarter'
								? 'bg-gray-900 text-white shadow-sm'
								: 'text-gray-700 hover:text-gray-900'
						}`}
					>
						Quarter
					</button>
					<button
						type="button"
						onclick={() => (timeRange = 'ytd')}
						aria-pressed={timeRange === 'ytd'}
						class={`w-1/3 px-3 py-2 rounded-md text-sm font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-gray-900 ${
							timeRange === 'ytd'
								? 'bg-gray-900 text-white shadow-sm'
								: 'text-gray-700 hover:text-gray-900'
						}`}
					>
						YTD
					</button>
				</div>
			</fieldset>
		</div>

		<div class="mt-4 flex flex-wrap items-center justify-between gap-2 text-xs text-gray-500">
			<span>
				Selected: <span class="font-medium text-gray-700">{memberOptions.find((m) => m.id === selectedMemberId)?.name}</span>
			</span>
			<span>
				View: <span class="font-medium text-gray-700">{view}</span> · Range:{' '}
				<span class="font-medium text-gray-700">{timeRange.toUpperCase()}</span>
			</span>
		</div>
	</div>

	<!-- Summary Cards Grid -->
	<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
		<!-- Card 1: Total Rebates -->
		<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
			<div class="flex items-center">
				<div class="p-2 bg-green-100 rounded-lg">
					<svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1"></path>
					</svg>
				</div>
				<div class="ml-4">
					<p class="text-sm font-medium text-gray-600">Total Rebates Earned</p>
					<p class="text-2xl font-bold text-gray-900">$3,247</p>
					<p class="text-sm text-green-600 mt-1">+12% from last month</p>
				</div>
			</div>
		</div>

		<!-- Card 2: YTD Purchases -->
		<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
			<div class="flex items-center">
				<div class="p-2 bg-blue-100 rounded-lg">
					<svg class="w-6 h-6 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"></path>
					</svg>
				</div>
				<div class="ml-4">
					<p class="text-sm font-medium text-gray-600">YTD Purchases</p>
					<p class="text-2xl font-bold text-gray-900">$58,420</p>
					<p class="text-sm text-blue-600 mt-1">+8% from last year</p>
				</div>
			</div>
		</div>

		<!-- Card 3: Active Vendors -->
		<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
			<div class="flex items-center">
				<div class="p-2 bg-purple-100 rounded-lg">
					<svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
					</svg>
				</div>
				<div class="ml-4">
					<p class="text-sm font-medium text-gray-600">Active Vendors</p>
					<p class="text-2xl font-bold text-gray-900">7</p>
					<p class="text-sm text-gray-500 mt-1">2 new this month</p>
				</div>
			</div>
		</div>

		<!-- Card 4: Average Rebate Rate -->
		<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
			<div class="flex items-center">
				<div class="p-2 bg-orange-100 rounded-lg">
					<svg class="w-6 h-6 text-orange-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
					</svg>
				</div>
				<div class="ml-4">
					<p class="text-sm font-medium text-gray-600">Avg Rebate Rate</p>
					<p class="text-2xl font-bold text-gray-900">5.6%</p>
					<p class="text-sm text-orange-600 mt-1">+0.3% from last quarter</p>
				</div>
			</div>
		</div>
	</div>

	<!-- Placeholder Content Area -->
	<div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
		{#if view === 'member'}
			<!-- Recent Purchases (matches MVP spec) -->
			<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
				<h3 class="text-lg font-semibold text-gray-900 mb-4">Recent Purchases</h3>
				<div class="overflow-x-auto">
					<table class="min-w-full text-sm">
						<thead>
							<tr class="text-left text-xs font-semibold uppercase tracking-wide text-gray-500 border-b">
								<th class="py-3 pr-4">Date</th>
								<th class="py-3 pr-4">Vendor</th>
								<th class="py-3 pr-4 text-right">Amount</th>
								<th class="py-3 text-right">Rebate Earned</th>
							</tr>
						</thead>
						<tbody>
							{#each purchases as p, i}
								<tr class={i < purchases.length - 1 ? 'border-b border-gray-100' : ''}>
									<td class="py-3 pr-4 text-gray-700">{p.date}</td>
									<td class="py-3 pr-4 font-medium text-gray-900">{p.vendor}</td>
									<td class="py-3 pr-4 text-right tabular-nums text-gray-900">
										{formatUsd(p.amount)}
									</td>
									<td class="py-3 text-right tabular-nums text-green-700">
										{formatUsd(p.rebateEarned)}
									</td>
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
				<p class="mt-3 text-xs text-gray-500">
					Placeholder rows. Later this will be filtered by member + time range and loaded from a data
					layer.
				</p>
			</div>
		{:else}
			<!-- Top Suppliers (Executive MVP spec) -->
			<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
				<h3 class="text-lg font-semibold text-gray-900 mb-4">Top Suppliers</h3>
				<div class="overflow-x-auto">
					<table class="min-w-full text-sm">
						<thead>
							<tr class="text-left text-xs font-semibold uppercase tracking-wide text-gray-500 border-b">
								<th class="py-3 pr-4">Supplier</th>
								<th class="py-3 pr-4 text-right">Volume</th>
								<th class="py-3 text-right">Share</th>
							</tr>
						</thead>
						<tbody>
							{#each topSuppliers as s, i}
								<tr class={i < topSuppliers.length - 1 ? 'border-b border-gray-100' : ''}>
									<td class="py-3 pr-4 font-medium text-gray-900">{s.supplier}</td>
									<td class="py-3 pr-4 text-right tabular-nums text-gray-900">
										{formatUsd(s.volume)}
									</td>
									<td class="py-3 text-right tabular-nums text-gray-700">{s.share}%</td>
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
				<p class="mt-3 text-xs text-gray-500">Placeholder rows. Later: drill-down + time filters.</p>
			</div>
		{/if}

		<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
			<h3 class="text-lg font-semibold text-gray-900 mb-4">
				{view === 'member' ? 'Monthly Purchases Trend' : 'Supplier Mix'}
			</h3>
			<div class="h-64 flex items-center justify-center bg-gray-50 rounded-lg border-2 border-dashed border-gray-300">
				<div class="text-center">
					<svg class="w-12 h-12 text-gray-400 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
					</svg>
					<p class="text-sm text-gray-500">Chart will be implemented here</p>
					<p class="text-xs text-gray-400 mt-1">
						{view === 'member'
							? 'Line/area chart (Jan–Dec amounts)'
							: 'Doughnut/bar chart (Supplier A/B/C mix)'}
					</p>
				</div>
			</div>
		</div>
	</div>

	{#if view === 'executive'}
		<!-- Executive KPI Row (visible when Executive view selected) -->
		<div class="mt-2">
			<h2 class="text-lg font-semibold text-gray-900 mb-3">Executive Summary</h2>
			<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
				<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
					<p class="text-sm font-medium text-gray-600">Total GPO Volume</p>
					<p class="text-2xl font-bold text-gray-900">{formatUsd(4200000)}</p>
					<p class="text-sm text-gray-500 mt-1">All members · {timeRange.toUpperCase()}</p>
				</div>
				<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
					<p class="text-sm font-medium text-gray-600">Average Rebate Rate</p>
					<p class="text-2xl font-bold text-gray-900">6.1%</p>
					<p class="text-sm text-gray-500 mt-1">Weighted average</p>
				</div>
				<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
					<p class="text-sm font-medium text-gray-600">Top Supplier Share</p>
					<p class="text-2xl font-bold text-gray-900">38%</p>
					<p class="text-sm text-gray-500 mt-1">Supplier A</p>
				</div>
			</div>
		</div>
	{/if}
</div>
