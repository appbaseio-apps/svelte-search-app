<script context="module">
	export const prerender = true;
</script>

<script>
	import SearchBox from '$lib/SearchBox.svelte';
	import Facet from '$lib/Facet.svelte';
	import Result from '$lib/Result.svelte';
	import { setContext } from 'svelte';
	import { SearchBase } from '@appbaseio/searchbase';

	// Instantiate the `SearchBase`
	const searchbase = new SearchBase({
		// Elasticsearch index name
		index: 'gitxplore-app',
		// Appbase credentials
		credentials: '0f448043059d:a4264120-cd7a-4574-a449-2c5df0523cb1',
		url: 'https://appbase-demo-ansible-abxiydt-arc.searchbase.io'
	});

	setContext('searchContext', {
		searchbase
	});
</script>

<svelte:head>
	<title>Search App</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<SearchBox />
</section>
<!--Changed by Shubham-->
<div class="row">
	<div class="col sidebar">
		<Facet />
	</div>
	<div class="col content">
		<Result/>
	</div>
</div>
<!--Changed by Shubham-->

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
	/**
	Changed by Shubham below
	*/
	.sidebar {
		border-right: 1px solid #ccc;
		max-width: 400px;
	}
	.content{
	background: #fafafa;
	}
</style>
