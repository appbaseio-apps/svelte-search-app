<script>
	import { getContext } from 'svelte';
	const { searchbase } = getContext('searchContext');
	// @ts-ignore
import AutoComplete from 'simple-svelte-autocomplete';
	import Icon from './Icons.svelte';
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

	/**
	 * @type {object}
	 */
	let selectedItem;

	/**
	 * @param {string} keyword
	 */
	async function getSuggestions(keyword) {
		console.log('getSuggestion');
		// Set the value to fetch the suggestions
		searchComponent.setValue(keyword, { triggerDefaultQuery: false });
		const results = (await searchComponent.triggerDefaultQuery())?.hits?.hits ?? [];

		return results;
	}

	/**
	 * @param {object} item
	 */
	function onChange(item) {
		selectedItem = item;
		// @ts-ignore
		searchComponent.setValue(item?.value, { triggerDefaultQuery: false });
		searchComponent.triggerDefaultQuery();
		searchComponent.triggerCustomQuery();
	}
</script>

<div class="autocomplete-wrapper">
	<div class="search-icon-wrapper"><Icon /></div>
	<AutoComplete
		showClear={true}
		searchFunction={getSuggestions}
		delay={500}
		localFiltering={false}
		showLoadingIndicator={true}
		labelFieldName="value"
		valueFieldName="value"
		bind:selectedItem
		placeholder="Searh Git..."
		{onChange}
		hideArrow={true}
		minCharactersToSearch={0}
		><div slot="item" let:item class="suggestion-item">
			<Icon suggestionType={item._suggestion_type} />
			<div class="trim">{@html item.label}</div>
		</div>
	</AutoComplete>
</div>

<style>
	.autocomplete-wrapper {
		position: relative;
		width: max-content;
		margin: auto;
	}
	:global(.autocomplete-wrapper .search-icon-wrapper svg) {
		position: absolute;
		z-index: 3;
		top: 50%;
		left: 12px;

		transform: translateY(-50%) scale(1.35);
		fill: #0b6aff;
	}
	.autocomplete-wrapper :global(.autocomplete) {
		width: min(100vw, 500px);
		height: 40px;
	}

	.autocomplete-wrapper :global(.autocomplete-list) {
		overflow-y: auto;
		max-height: min(100vh, 435px);
		border: none;
		border-radius: 6px;
		border-top-left-radius: 0;
		border-top-right-radius: 0;
		box-shadow: rgb(0 0 0 / 20%) 0px 10px 15px;
		border-top: 1px solid #f2f0f0;
	}
	.autocomplete-wrapper :global(.autocomplete-list-item) {
		padding: 12px 10px;
	}
	.autocomplete-wrapper :global(.autocomplete-list-item.selected) {
		transition: all 0.3s ease-in;
		background: #2d84f6;
	}
	.autocomplete-wrapper :global(.autocomplete-list-item.selected svg) {
		fill: white !important;
		transition: fill 0.3s ease-in;
	}
	.autocomplete-wrapper :global(.autocomplete-list-item svg) {
		width: 20px;
	}
	.autocomplete-wrapper :global(.input-container) {
		height: 100%;
	}
	.autocomplete-wrapper :global(.input-container input) {
		border: none;
		outline: none;
		border-radius: 6px;
		box-shadow: rgb(0 0 0 / 20%) 0px 0px 6px;
		overflow: hidden;
		height: 100%;
		padding-left: 2.2rem;
	}
	.autocomplete-wrapper :global(.input-container input:focus) {
		box-shadow: rgb(0 0 0 / 20%) 0px 0px 15px;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}
	.autocomplete-wrapper :global(.autocomplete-clear-button) {
		opacity: 1;
		position: relative;
		color: white;
	}
	.autocomplete-wrapper :global(.autocomplete-clear-button::after) {
		position: absolute;
		content: '\2715';
		color: black;
		right: 7px;
	}

	.suggestion-item {
		display: flex;
		align-items: center;
		gap: 10px;
	}
	.trim {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}
</style>
