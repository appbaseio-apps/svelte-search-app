<script>
	import { getContext, onMount } from 'svelte';
	import Loader from './Loader.svelte';
	const { searchbase } = getContext('searchContext');
	// Register result component with react dependency on search and filter component => To render the results
	const resultComponent = searchbase.register('result-component', {
		dataField: 'name',
		react: {
			and: ['search-component', 'language-filter']
		},
		defaultQuery: () => ({
			track_total_hits: true
		})
	});

	//Svelte state variables
	let results = [];
	let resultStats = null;
	let isLoading = false;
	// Build UI to display language options
	resultComponent.subscribeToStateChanges(
		(change) => {
			console.log('change.', change);
			if (change.requestStatus) {
				isLoading = change.requestStatus.next === 'PENDING';
			}
			if (change.results) {
				results = change.results.next.data;
				let { numberOfResults, time } = change.results.next;
				resultStats = { numberOfResults, time };
			}
		},
		['results', 'requestStatus']
	);

	onMount(() => {
		resultComponent.triggerDefaultQuery();
	});
</script>

<div class="results">
	{#if isLoading}
		<div class="loader-wrapper">
			<Loader />
		</div>
	{:else}
		{#if resultStats}
			<p class="restuls-stats">
				Showing {resultComponent.results.numberOfResults} in {resultComponent.results.time} ms
			</p>
		{/if}

		{#each results as i (i._id)}
			<div class="result">
				<div class="image">
					<img src={i.avatar} alt={i.name} />
				</div>
				<div class="details">
					<h4 title={i.name}>{i.name}</h4>
					<p title={i.description}>{i.description}</p>
				</div>
			</div>
		{/each}
	{/if}
</div>

<style>
	.results {
		padding: 10px;
		background: #ffffff;
		width: 100%;
		min-height: 50px;
		padding-top: 4px;
	}
	.loader-wrapper {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 300px;
	}
	.restuls-stats {
		text-align: center;
	}
	.result {
		display: flex;
		background: white;
		margin: 10px 0;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
		border-radius: 6px;
		overflow: hidden;
	}
	.result:first {
		padding-top: 0;
	}
	.result .image {
		display: flex;
		padding: 6px;
	}
	.result img {
		height: 150px;
		width: 150px;
		background-size: cover;
	}

	.result .details {
		padding-left: 20px;
	}

	.result h4 {
		font-weight: bold;
		margin-bottom: 6px;
	}
	.details p {
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 3;
		-webkit-box-orient: vertical;
		padding-right: 1rem;
	}
</style>
