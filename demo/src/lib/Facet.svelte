<script>
	import { getContext, onMount } from 'svelte';
	const { searchbase } = getContext('searchContext');
	// Register filter component with dependency on search component
	const filterComponent = searchbase.register('language-filter', {
		// The type property as `term` is to use the Elasticsearch terms aggregations.
		type: 'term',
		dataField: 'language.keyword'
	});

	//Svelte state variables
	let aggregations = [];
	// Build UI to display language options
	filterComponent.subscribeToStateChanges(
		(change) => {
			aggregations = change.aggregationData.next.data;	
		},
		['aggregationData']
	);

	onMount(() => {
		filterComponent.triggerDefaultQuery();
	});
</script>

<div class="filter">
	<h1>Languages</h1>
	<div id="language-filter" class="container">
		{#each aggregations as aggregation (aggregation._key)}
			<label class="filter__input">
				<input type="checkbox" class="filter__check" name={aggregation._key} value={aggregation._key} />
				<span class="text">{aggregation._key}</span>
				<span class="doc_count">{aggregation._doc_count}</span>
			</label>
		{/each}
	</div>
</div>

<style>
	.filter{
		width: 100%;
	}
	.filter__check {
		position: relative;
		width: min-content;
		margin: 0.5rem;
	}
	.text{
		flex: 1;
	}
	.filter__input{
		display: flex;
		justify-content: flex-start;
		width: 100%;
		align-items: center;
		user-select: none;
		cursor: pointer;
	}
	.doc_count{
		justify-self: flex-end;
		color: #9d9d9d;
	}
	.filter h1{
		display: flex;
		box-sizing: border-box;
		padding: 16px;
		align-items: center;
		font-family: Roboto, "Helvetica Neue", sans-serif;
		font-size: 14px;
		font-weight: 500;
		color: rgba(0,0,0,.54);
		margin: 0;
		height: 48px;
		line-height: 16px;
		margin-top: -8px;
	}
</style>
