<script>
  import { getContext, onMount } from "svelte";
  import InfiniteScroll from "./InfiniteScroll.svelte";
  import Loader from "./Loader.svelte";
  const { searchbase } = getContext("searchContext");
  // Register result component with react dependency on search and filter component => To render the results
  const resultComponent = searchbase.register("result-component", {
    dataField: "name",
    react: {
      and: ["search-component", "language-filter"],
    },
    defaultQuery: () => ({
      track_total_hits: true,
    }),
    preserveResults: true,
  });

  //Svelte state variables
  let results = [];
  let resultStats = null;
  let isLoading = false;
  let page = 0;
  // Build UI to display language options
  resultComponent.subscribeToStateChanges(
    (change) => {
      console.log("change.", change);
      if (change.requestStatus) {
        isLoading = change.requestStatus.next === "PENDING";
      }
      if (change.results) {
        results = change.results.next.data;
        let { numberOfResults, time } = change.results.next;
        resultStats = { numberOfResults, time };
      }
    },
    ["results", "requestStatus"]
  );

  onMount(() => {
    // fetch results for the controller
    resultComponent.triggerDefaultQuery();
  });

  const formatNumber = (number) => {
    return new Intl.NumberFormat("en-IN", {
      maximumSignificantDigits: 3,
    }).format(number);
  };
</script>

<div class="results">
  {#if isLoading}
    <div class="loader-wrapper">
      <Loader />
    </div>
  {/if}
  {#if resultStats}
    <p class="restuls-stats">
      Showing {formatNumber(resultStats.numberOfResults)} in {resultStats.time} ms
    </p>
  {/if}

  {#each results as i (i._id)}
    <div class="result">
      {#if i.language}
        <span class="tag">{i.language}</span>
      {/if}
      <div class="image">
        <img src={i.avatar} alt={i.name} />
      </div>
      <div class="details">
        <h4 title={i.name}>{i.name}</h4>
        <p title={i.description}>{i.description}</p>
        <div class="bottom">
          <span>⭐️ {formatNumber(i.stars)}</span>

          <a target="_blank" href={i.url}> 🔗 View on GitHub</a>
        </div>
      </div>
    </div>
  {/each}
  <InfiniteScroll
    hasMore={(page + 1) * 10 < resultStats?.numberOfResults}
    threshold={100}
    on:loadMore={() => {
      page++;
      resultComponent.setFrom(page * 10, { triggerDefaultQuery: true });
    }}
  />
</div>

<style>
  :root {
    box-sizing: border-box;
  }
  .results {
    padding: 10px;
    background: #ffffff;
    width: 100%;
    min-height: 400px;
    height: max(78vh, 400px);
    overflow: auto;
    position: relative;
  }
  .loader-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    min-height: 400px;
    position: sticky;
    width: 100%;
    left: 0;
    top: 0;
    background: #ffffffbd;
    z-index: 2;
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
    flex-wrap: wrap;
    width: 100%;
    position: relative;
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
    flex: 1;
    min-width: 300px;

    padding-bottom: 5px;
  }

  .result h4 {
    font-weight: bold;
    margin-bottom: 6px;
    text-transform: capitalize;
  }
  .details p {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    padding-right: 1rem;
    height: 47px;
    line-height: 22px;
  }

  .bottom {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-right: 13px;
  }

  .bottom a {
    text-decoration: none;
  }
  .tag {
    position: absolute;
    background: #0a0a22;
    display: inline-block;
    padding: 3px 5px;
    border-radius: 4px;
    color: white;
    top: 12px;
    right: 12px;
  }
</style>
