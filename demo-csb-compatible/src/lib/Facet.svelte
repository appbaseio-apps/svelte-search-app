<script>
	import { getContext, onMount } from 'svelte';
	import Loader from './Loader.svelte';
	const { searchbase } = getContext('searchContext');
	// Register filter component with dependency on search component
	const filterComponent = searchbase.register('language-filter', {
		// The type property as `term` is to use the Elasticsearch terms aggregations.
		type: 'term',
		dataField: 'language.keyword',
		react: {
			and: ['search-component']
		}
	});

	//Svelte state variables
	let aggregations = [];
	let isLoading = false;
	// Build UI to display language options
	filterComponent.subscribeToStateChanges(
		(change) => {
			if (change.requestStatus) {
				isLoading = change.requestStatus.next === 'PENDING';
			}
			if (change.aggregationData) {
				aggregations = change.aggregationData.next.data;
			}
		},
		['aggregationData', 'requestStatus']
	);

	onMount(() => {
		filterComponent.triggerDefaultQuery();
	});

	function handleSelect(e, i) {
		const values = filterComponent.value || [];
		if (values && values.includes(i._key)) {
			values.splice(values.indexOf(i._key), 1);
		} else {
			values.push(i._key);
		}
		// Set filter value and trigger custom query
		filterComponent.setValue(values, {
			triggerDefaultQuery: false,
			triggerCustomQuery: true
		});
	}
</script>

<div class="filter">
	<h1>Languages</h1>
	<div id="language-filter" class="container">
		{#if isLoading}
			<div class="loader-wrapper">
				<Loader />
			</div>
		{:else}
			{#each aggregations as aggregation (aggregation._key)}
				{#if aggregation._key}
					<label class="filter__input">
						<input
							type="checkbox"
							on:click={(e) => handleSelect(e, aggregation)}
							class="filter__check"
							name={aggregation._key}
							value={aggregation._key}
						/>
						<span class="text">{aggregation._key}</span>
						<span class="doc_count">{aggregation._doc_count}</span>
					</label>
				{/if}
			{/each}
		{/if}
	</div>
</div>

<style>
	.loader-wrapper {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 300px;
	}
	.filter {
		width: 100%;
		box-shadow: 0 1px 3px rgb(0 0 0 / 12%), 0 1px 2px rgb(0 0 0 / 24%);
		border-radius: 6px;
		overflow: hidden;
		padding: 1rem;
	}
	.filter__check {
		position: relative;
		width: min-content;
		margin: 0.5rem;
	}
	.text {
		flex: 1;
	}
	.filter__input {
		display: flex;
		justify-content: flex-start;
		width: 100%;
		align-items: center;
		user-select: none;
		cursor: pointer;
	}
	.doc_count {
		justify-self: flex-end;
		color: #9d9d9d;
	}
	.filter h1 {
		display: flex;
		box-sizing: border-box;
		padding: 16px;
		align-items: center;
		font-family: Roboto, 'Helvetica Neue', sans-serif;
		font-size: 17px;
		font-weight: 500;
		color: rgb(10 10 34);
		margin: 0;
		height: 48px;
		line-height: 16px;
		margin-top: -8px;
		padding-left: 7px;
	}
</style>
