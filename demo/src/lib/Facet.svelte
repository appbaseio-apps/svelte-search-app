<script>
	import { getContext, onMount } from 'svelte';
	const { searchbase } = getContext('searchContext');
	// Register search component => To render the suggestions
	const searchComponent = searchbase.register('search-component', {
		enablePredictiveSuggestions: true,
		dataField: ['name', 'description', 'name.raw', 'fullname', 'owner', 'topics'],
		type: 'suggestion',
		enableRecentSuggestions: true,
		popularSuggestionsConfig: { size: 3, minChars: 2, index: 'good-books-ds' },
		recentSuggestionsConfig: { size: 3, index: 'good-books-ds', minChars: 4 },
		size: 10
	});

	// Register filter component with dependency on search component
	const filterComponent = searchbase.register('language-filter', {
		// The type property as `term` is to use the Elasticsearch terms aggregations.
		type: 'term',
		dataField: 'language.keyword'
	});
	// Build UI to display language options
	filterComponent.subscribeToStateChanges(
		(change) => {
			const aggregations = change.aggregationData.next;
			const container = document.getElementById('language-filter');
			if (container) {
				container.innerHTML = '';
				aggregations.data.forEach((i) => {
					if (i._key) {
						const checkbox = document.createElement('input');
						checkbox.type = 'checkbox';
						checkbox.name = i._key;
						checkbox.id = i._key;
						checkbox.onclick = () => {
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
						};
						const label = document.createElement('label');
						label.htmlFor = i._key;
						label.innerHTML = `${i._key}(${i._doc_count})`;
						const div = document.createElement('div');
						div.appendChild(checkbox);
						div.appendChild(label);
						container.appendChild(div);
					}
				});
			}
		},
		['aggregationData']
	);

	onMount(() => {
		filterComponent.triggerDefaultQuery();
	});
</script>

<div id="language-filter" class="filter" />

<style>
	.filter :global(label) {
		padding-left: 5px;
		height: 30px;
		line-height: 2;
	}

	.filter :global(input) {
		position: relative;
		top: 2px;
	}
	.filter :global(div) {
		padding-bottom: 5px;
	}
</style>
