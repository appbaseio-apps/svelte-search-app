<script>
	import SearchBox from "./lib/SearchBox.svelte";
	import Facet from "./lib/Facet.svelte";
	import Results from "./lib/Results.svelte";
	import { setContext } from "svelte";
	// @ts-ignore
import { SearchBase } from "@appbaseio/searchbase";

	// Instantiate the `SearchBase`
	const searchbase = new SearchBase({
	  // Elasticsearch index name
	  index: "gitxplore-app",
	  // Appbase credentials
	  credentials: "0f448043059d:a4264120-cd7a-4574-a449-2c5df0523cb1",
	  url: "https://appbase-demo-ansible-abxiydt-arc.searchbase.io"
	});

	setContext("searchContext", {
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
<div class="row">
	<div class="col sidebar">
		<Facet />
	</div>
	<div class="col content">
		<Results />
	</div>
</div>

<style>
	section {
	  width: 100%;
	  text-align: center;
	  padding: 2rem;
	  box-sizing: border-box;
	}

	h1 {
	  width: 100%;
	}

	.row {
	  display: flex;
	  align-items: flex-start;
	  justify-content: space-between;
	  flex-wrap: wrap;
	  gap: 3rem;
	  width: 100%;
	  max-width: 97%;
	}

	.sidebar {
	  max-width: 300px;
	  flex: 1;
	  margin: 1rem auto;
	}
	.content {
	  flex: 1;
	  background: #fafafa;
	}
	* {
	  box-sizing: border-box;
	}
	:root {
	  box-sizing: border-box;
	  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto",
	    "Noto Sans", "Ubuntu", "Droid Sans", "Helvetica Neue", sans-serif;
	}
	body {
	  background: white;
	  overflow-x: hidden;
	}
	main {
	}

	footer {
	  display: flex;
	  flex-direction: column;
	  justify-content: center;
	  align-items: center;
	  padding: 40px;
	}

	footer a {
	  font-weight: bold;
	}

	@media (min-width: 480px) {
	  footer {
	    padding: 40px 0;
	  }
	}
</style>
