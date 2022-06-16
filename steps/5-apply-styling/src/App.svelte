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
    index: "git-explore-2022",
    // Appbase credentials
    credentials: "22b0a73dc385:437b1942-e3a9-46aa-8438-c307fe0a8eab",
    url: "https://appbase-demo-ansible-abxiydt-arc.searchbase.io",
  });

  setContext("searchContext", {
    searchbase,
  });
</script>

<svelte:head>
  <title>GitHub Search - Svelte App</title>
  <meta name="description" content="Svelte demo app" />
</svelte:head>
<nav>
  <img
    src="https://miro.medium.com/max/700/0*lPloL8w-ZKtjxeB5.png"
    alt="svelte logo"
  />
  <h1>GitHub Search</h1>
  <a
    href="https://blog.reactivesearch.io/search-ui-with-svelte-and-elasticsearch"
    target="_blank">🔗 Read how it's built</a
  >
</nav>
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
  body {
    margin: 0;
  }
  section {
    width: 100%;
    text-align: center;
    padding: 2rem;
    box-sizing: border-box;
  }

  nav {
    display: flex;
    background: #fe3e0012;
    align-items: center;
    justify-content: space-between;
    padding: 10px;
    flex-wrap: wrap;
    gap: 1rem;
  }
  nav img {
    height: 80px;
    position: relative;
    top: -3px;
  }

  nav a {
    text-decoration: none;
    font-size: 1.5rem;
    color: #1c07ee;
  }
  nav h1 {
    width: max-content;
    font-size: 1.7rem;
    color: #fe3e00;
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
    max-width: 95vw;
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
