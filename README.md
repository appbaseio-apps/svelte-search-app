# GitHub Search App with Svelte.js

<img width="800" alt="Screenshot_2022-06-14_at_3 03 49_PM_1600x840" src="https://user-images.githubusercontent.com/57627350/173546733-2d8801d8-b136-431e-8ace-fc7c0dcf4772.png">


In this repo, we are going to build a Github search Application using [Svelte.js](https://svelte.dev/) and Elasticsearch. Svelte is a new way to build web applications. It's a compiler that takes your declarative components and converts them into efficient JavaScript that surgically updates the DOM. Elasticsearch is the living heart of what is today’s most popular search engine. Elasticsearch’s role is so central that it has become synonymous with the name of the *SEARCH* itself. Primarily for search and log analysis, Elasticsearch is today one of the most popular database systems available today. In any case, we will do a walkthrough explaining the basics of Search and build the app in a step-by-step manner.


Before diving in, you can try out the Live Demo of the final app [here](https://tuned-using-appbase-dashboard.mohdashraf01089.repl.co/).

# **What is ElasticSearch**

ElasticSearch is a *blazing fast, open-source, full-text search engine*. It allows you to **store**, **search**, and **analyze** both small and large volumes of data quickly (we are talking milliseconds here). It is generally used as the underlying engine/technology to power applications that have complex search features and requirements. You can read more about it [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html).

# **Why ElasticSearch**

With ElasticSearch, you can build a fast search that utilizes its powerful Query DSL. However, setting up ElasticSearch correctly requires a lot of work. For instance, the mappings, analyzers, and tokenizers need to be set correctly or you may not receive accurate search results back. Besides, the more filters that get applied along with the search query, the more complex the resulting search query becomes.

# **Setting Up ElasticSearch**

Setting up and maintaining our own search engine cluster can be time-consuming and costly. So we will be using **Appbase.io** to save our time and cost of maintaining Cluster.

At appbase.io, we have built some open-source tools to help you do all these things within a matter of clicks.

- Tool to **add data** into ElasticSearch — [Importer](http://importer.appbase.io/)
- Tool to **view ElasticSearch data** like an excel sheet — [Data Browser](https://opensource.appbase.io/dejavu/)
- Tool to **generate relevant ElasticSearch queries** easily — [Query Builder](https://opensource.appbase.io/mirage/)

In this blog post, we will be using these tools to utilize the strength of ElasticSearch with Vue to build powerful apps.


# **Building our Search Backend**

As mentioned above, we will be using [appbase.io](https://appbase.io/) for our search backend. It offers a real-time search API service based on ElasticSearch. This saves us time and the cost of setting up and maintaining our own search engine cluster.

*This means that we wouldn’t require writing any server code. 💥*

In order to make a Github search application, we will need a dataset of Github repositories. I have already created an [appbase.io](https://appbase.io/) cluster with the GitHub dataset indexed over [here.](https://dejavu.appbase.io/?appname=git-explore-2022&url=https://22b0a73dc385:437b1942-e3a9-46aa-8438-c307fe0a8eab@appbase-demo-ansible-abxiydt-arc.searchbase.io&mode=view) You can either clone this dataset to use your own credentials or copy the below credentials:

```

index="git-explore-2022"
credentials="22b0a73dc385:437b1942-e3a9-46aa-8438-c307fe0a8eab"
url="https://appbase-demo-ansible-abxiydt-arc.searchbase.io"
```

## **How to use ElasticSearch with Svelte.js?**

We will be using the [Searchbase](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/QuickStart/) open-source library to build the Github search application in this post. [Searchbase](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/QuickStart/) is a lightweight platform-agnostic library to build search applications with Elasticsearch. It provides scaffolding to build UIs such as Airbnb, Yelp, you name it. We will show how simple it is to build one by creating our Github search app.

# Setting Up Frontend


# Step 1: Base setup of Svelte.js with Searchbase

We will use [Replit](https://replit.com/~) to help us build our application in a step-by-step fashion. Open the above link and create a new Svelte template app.


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654601296403/6iWpcnE9N.png align="left")

Alternatively, you can set up a [SvelteKit](https://kit.svelte.dev/docs/introduction#what-is-sveltekit) app locally. Read more about it [here](https://kit.svelte.dev/docs/introduction#getting-started).

**Install Searchbase**

Now we can add our dependency by clicking the `Add Dependency` button on CodeSandbox and searching for `searchbase`, or if you are working locally you can install the package:


```
npm i @appbaseio/searchbase
``` 


# Step 2: Configure search Backend

In this step, we would import the `@appbaseio/searchbase` library in the `App.svelte` file to create the search controllers. The `searchbase` library exports two classes `SearchBase` and `SearchComponent`. The `SearchComponent` class acts like a search controller that provides necessary utilities to build search interfaces, for example, to execute Elasticsearch queries and read search/aggregations responses.

If you're using multiple search UI components which can have a dependency on each other then use the `SearchBase` class to initialize a centralized store and register search controllers(`SearchComponent`) for each search UI component.


### Connecting our Search Backend via SearchBase Class

In this demo we're going to build three search components:

- A component to display relevant search suggestions.
- A component to select languages to filter the results.
- In the end, we need a component to display the results.

Each search component would be controlled by an instance of the SearchComponent class. So, we'll use the `Searchbase` class to connect the search backend for all the controllers in a single place.


```
<script>
	import { setContext } from 'svelte';
	import { SearchBase } from '@appbaseio/searchbase';

	// Instantiate the `SearchBase`
	const searchbase = new SearchBase({
		// Elasticsearch index name
		index: 'git-explore-2022',
		// Appbase credentials
		credentials: '22b0a73dc385:437b1942-e3a9-46aa-8438-c307fe0a8eab',
		url: 'https://appbase-demo-ansible-abxiydt-arc.searchbase.io'
	});

	setContext('searchContext', {
		searchbase
	});

</script>
``` 
*Inside App.svelte*

> Notice, how we make use of the Svelte context API to make the SearchBase instance available to the child components. Learn more about the Svelte Context API [here](https://svelte.dev/tutorial/context-api).


<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@mohdashraf01089/configure-search-backend?embed=true"></iframe>


# Step 3: Build Search UI

Now, we have the `SearchBase` instance configured. Let's start building the search interface by adding search controllers.

## Build Search Component with Autosuggestions

The first component is going to be a Text Input with autocomplete UI.


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654254333729/rdq6_Lmjk.png align="left")

### Define search controller

We create a new Svelte file, SearchBox.svelte for declaring the search controller, handling the search suggestions UI and query logic.

To maintain the search state and establish the communication with search backend we have to register a search controller to the `SearchBase` instance in the following way:

```svelte

<script>
	import { getContext } from 'svelte';

    // extract out the searchbase instance from the context
	const { searchbase } = getContext('searchContext');
	
  // Register search component => To render the suggestions
  const searchComponent = searchbase.register("search-component", {
    		dataField: ['name', 'description', 'name.raw', 'fullname', 'owner', 'topics'],
    includeFields: [
      "name",
      "description",
      "owner",
      "fullname",
      "language",
      "topics",
    ],
    type: "suggestion",
    enableRecentSuggestions: true,
    popularSuggestionsConfig: { size: 3, minChars: 2 },
    recentSuggestionsConfig: { size: 3, minChars: 4 },
    size: 10,
  });

</script>

```
*Inside lib/SearchBox.svelte*

Here, we have registered a new controller with a unique identifier(we'll refer to it as controller id) named `search-component`. The `id` must be unique to each controller and it is helpful to define the reactivity among controllers. We'll discuss it later.

The `dataField` property values are the name of the fields on which we want to apply our search and `size` represents the number of suggestions to display. Our final goal is to build a relevant search experience so users can find the desired results in a few interactions. To improve the search relevancy we're using multiple data fields with weights, field weights allow weighted search for the index fields. A higher number implies a higher relevance weight for the corresponding field in the search results. A searchable data field has many options to build various kinds of search experiences, some of the most used options are:

- `keyword`: Searches the exact value of the field. You typically want to enable this and provide it with the highest weight.
- `autosuggest`: Searches on the prefix value of the field. Enable this when you want users to do an autocomplete/suggestions search on the field. You should set a relatively lower weight for it.
- `search`: Searches on an infix value of the field. Enable this when you want users to be able to find results by entering partial values. You should set a relatively lower weight for it.

You can try out the different field configurations and choose the best one by using the Appbase [Searchrelevancy control plane](https://docs.appbase.io/docs/search/relevancy).


### Build Autosuggestions UI

To build the auto-suggestions UI we’re going to use [`simple-svelte-autocomplete`](https://www.npmjs.com/package/simple-svelte-autocomplete) library. You can install the library using the following command:

```
npm i simple-svelte-autocomplete
``` 

Let's integrate the Autocomplete UI using the [`simple-svelte-autocomplete`](https://www.npmjs.com/package/simple-svelte-autocomplete) library.

```
<script>
  ...
  import AutoComplete from "simple-svelte-autocomplete";

  // Register search component => To render the suggestions
  ...

  // keep track of the currently selected suggestion
  let selectedItem;

  // function to fetch suggestions from reactivesearch backend
  async function getSuggestions(keyword) {
    // Set the value of the search-component controller
    searchComponent.setValue(keyword, { triggerDefaultQuery: false });
    // triggerDefaultQuery makes an api call to fetch the suggestions
    const results =
      (await searchComponent.triggerDefaultQuery())?.hits?.hits ?? [];

    return results;
  }

  // event callback triggered when a suggestion from the
  // dropdown is selected
  function onChange(item) {
    // update the selectedItem variable
    selectedItem = item;
    // update the current value of the searchComponent controller
    searchComponent.setValue(item?.value, { triggerDefaultQuery: false });
    // triggerDefaultQuery makes an api call to fetch the suggestions
    searchComponent.triggerDefaultQuery();
  }
</script>

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
/>

``` 
> To understand the props used in the `<Autocomplete />` component, refer to the official docs [here](https://github.com/pstanoev/simple-svelte-autocomplete#properties).


Consider checking the full code and live preview using the below embed. 👇🏻

<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@mohdashraf01089/add-autocomplete-ui?embed=true"></iframe>


## Build Language Filter


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654607485953/p36lm_5R7.png align="left")

The second component we’re going to build is a language filter that would display the unique languages used by Github repositories with the ability to select/deselect them to filter the results.


Create a new Svelte file - `Facet.svelte` which would be used for the purpose.

### Define search controller

We have to create a new controller to manage the language filters.


```
  const { searchbase } = getContext("searchContext");
  // Register filter component with dependency on search component
  const filterComponent = searchbase.register("language-filter", {
    // The type property as `term` is to use the Elasticsearch terms aggregations.
    type: "term",
    dataField: "language.keyword",
    react: {
      and: ["search-component"],
    },
  });
``` 

The `filterComponent` controller uses the `term` type of query to get the aggregations from Elasticsearch to display the language options.

The `react` property allows components to watch each other and update their data reactively. We want to update the language options based on the `searchComponent` controller. We recommend [reading more](https://docs.appbase.io/docs/reactivesearch/v3/advanced/reactprop/) about the react property to understand the reactivity in depth.

### Get Initial Language Options

We want to display the language options at the start so we would fetch the data from Elasticsearch by calling the `triggerDefaultQuery` method of the `filterComponent` controller, on the mount of the component.


```
  import { onMount } from "svelte";

  onMount(() => {
    filterComponent.triggerDefaultQuery();
  });
``` 
### Build UI for language Filter

The UI is a pretty simple list of checkboxes that are multi-selectable. 


```

<div class="filter">
  <h1>Languages</h1>
  <div id="language-filter" class="container">
    {#if isLoading}
      <div class="loader-wrapper">
        Loading...
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
  // check for styles in the embed
</style>

``` 

We have bound the MultiList UI value to the `filterComponent` controller's value. We're reading the options from the `aggregationData` property of the `filterComponent` controller. To update the results we have to call the `triggerCustomQuery` method of `filterComponent` controller whenever language selection changes. Define the `handleSelection` method as below:

```
  // handleSelect method to control the current state of the selected languages
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
      triggerCustomQuery: true,
    });
  }
``` 

Also, the `language-filter` reacts to the value of the `search-component` controller. To reactively update the UI we need to subscribe to `aggregationData` and the `requestStatus`(optional to set the loading state).

The first change is to invoke [`triggerCustomQuery`](https://docs.appbase.io/docs/reactivesearch/react-native-searchbox/searchcomponent/#setters) from inside the `SearchBox.svelte` to reactively fetch language facets based on the selected suggestion value.

Inside SearchBox.svelte, make the below changes to the code:

```

  // reset language-filter controller
  const resetFilterComponent = () => {
    const facetInstance = searchbase.getComponents()["language-filter"];
    facetInstance?.setValue([], { triggerDefaultQuery: false });
  };

  // event callback triggered when a suggestion from the
  // dropdown is selected
  function onChange(item) {
    // update the selectedItem variable
    selectedItem = item;
    // reset language filters
    resetFilterComponent();
    // update the current value of the searchComponent controller
    searchComponent.setValue(item?.value, {
      triggerDefaultQuery: true, // triggerDefaultQuery makes an api call to fetch the suggestions
      triggerCustomQuery: true, // triggers api calls for dependent controllers
    });
  }

  onMount(() => {
    const listenInputValueChange = (e) => {
      if (!e.target.value) {
        resetFilterComponent();
        // reset search-component controller's value
        searchComponent.setValue("", {
          triggerDefaultQuery: true,
          triggerCustomQuery: true,
        });
      }
    };
    const inputElement =
      document.getElementsByClassName("autocomplete-input")[0];
    /* event listener */
    inputElement.addEventListener("input", listenInputValueChange);
  });
``` 

Back in Facet.svelte.

```
  //Svelte state variables
  let aggregations = [];
  let isLoading = false;
  // Build UI to display language options
  filterComponent.subscribeToStateChanges(
    (change) => {
      if (change.requestStatus) {
        isLoading = change.requestStatus.next === "PENDING";
      }
      if (change.aggregationData) {
        aggregations = change.aggregationData.next.data;
      }
    },
    ["aggregationData", "requestStatus"]
  );
``` 
> Read more about the subscription of properties [here](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/searchcomponent/#subscribe-to-the-properties-changes).

Combining the pieces our app looks like this at the moment.💡 Consider selecting a suggestion from the dropdown and watch how the language filter reacts.


<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@mohdashraf01089/add-language-filter?embed=true"></iframe>

## Build Results

The final component is to display the search results based on the search query and languages. The steps would be similar to what we have done with the previous components.


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654614375913/2P2IIA31M.png align="left")

Let's start by creating a new svelte file - `Results.svelte` and thereafter, follow the steps below.

### Define search controller


```
  // Register result component with react dependency on search and filter component => To render the results
  const resultComponent = searchbase.register("result-component", {
    dataField: "name",
    react: {
      and: ["search-component", "language-filter"],
    },
    defaultQuery: () => ({
      track_total_hits: true,
    }),
  });
``` 

The `resultComponent` controller watches the `search-component` and `language-filter` to update its data automatically whenever the user selects a value. The `defaultQuery` prop allows defining additional query params as per Elasticsearch Query DSL. We're sorting the results by the number of stars in descending order.

### Get Initial results

To display the results initially we fetch the results by invoking `triggerDefaultQuery()` on the mount of the component.


```
  onMount(() => {
    // fetch results for the controller
    resultComponent.triggerDefaultQuery();
  });
``` 

### Subscribing to the `results` and `requestStatus`

Similar to `search-component` & `language-filter`, here we need to subscribe to  `results` and `requestStatus`, properties to render the UI with updated results.


```
  //Svelte state variables
  let results = [];
  let resultStats = null;
  let isLoading = false;
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
``` 


### Build Results Layout

The UI is simply a list of cards displaying relevant GitHub repositories' information. Make the following changes in `Results.svelte` to render the results.


```
<div class="results">
  {#if isLoading}
    <div class="loader-wrapper">loading...</div>
  {:else}
    {#if resultStats}
      <p class="restuls-stats">
        Showing {resultComponent.results.numberOfResults} in {resultComponent
          .results.time} ms
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
          <div class="bottom">
            <span>⭐️ {i.stars}</span>
            <a href={i.url}> 🔗 View on GitHub</a>
          </div>
        </div>
      </div>
    {/each}
  {/if}
</div>

<style>
// refer to the embed link below for the CSS code
</style>
``` 

Summing up all the pieces, our app is now in a usable state. 🤩🌟


<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@mohdashraf01089/add-results-component?embed=true"></iframe>

# Step 4: Styling the Application

The functionality is working great, but the first impression is always what the user sees. At this point, We are ready to spice up the UI/ UX with the styling.

The CSS code can be individually referred from inside the Svelte components' files.
To sum up, we have,
- Improved the overall layout and UI of components.
- Added a loader to indicate API request state.
- Used custom icons for different types of suggestions; namely, recent, popular, index, promoted, etc.
- Implemented scroll-pagination for results.

<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@mohdashraf01089/search-app?embed=true"></iframe>

## Step 5: Build A Production Grade Search Experience

So far, we have gone through everything that’s needed to build a search interface. However, to make our search experience production-grade — we will make some tweaks below to set search relevance and result settings via appbase.io’s control plane instead of having them set via FE code. Setting them via a control plane allows one to tweak search relevance independently of the search interface changes.

## Using Rank Feature

[Elasticsearch Rank feature](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-rank-feature-query.html) query allows one to boost the relevancy score based on a numeric field. In this case, we will use Github stars to provide a default boost (stars field). This will do pretty useful things:

* When no text search criteria are defined, it will sort results by the highest stars,

* When a text search criteria are defined, the new relevance will be a combination of the text relevance plus a boost by stars.

We don’t want the rank feature to overpower the text relevance but still influence it in the absence of perfect matches, so we will use a log function here. You can read more about how it works over [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-rank-feature-query.html#rank-feature-query-logarithm). The function and the parameters to use really depend on your use-case, and we recommend tuning them by testing out with varied search terms.

You can configure the rank feature boost and parameters from the Search Settings section of the [appbase.io](http://appbase.io) dashboard and then use the **Test Search Relevancy** view to test it out.

![](https://cdn-images-1.medium.com/max/2870/0*u_m41NYObpVHLN-H.png)

## Configure Text Search Relevance with Appbase.io

The Search Settings section of the [appbase.io](http://appbase.io) dashboard also allows one to configure the searchable fields and weights. Setting these through the dashboard has the advantage of not having to set these values through code — thus allowing for easy experimentation and relevance optimization in real-time without requiring code deploys.

![](https://cdn-images-1.medium.com/max/2868/0*llps9x8DsiYyIJ9u.png)

## Configure Fields To Return From Result Settings

Elasticsearch source filtering allows filtering the response payload to only return relevant fields. Only returning the fields that are useful to the search UI results in a faster network request as well as better encapsulates your business data. For example, you may have a field containing sales data that you may want to utilize for creating search relevance but never return it to the search client.

![](https://cdn-images-1.medium.com/max/2878/0*NfSc-Z2Izu18Uu-2.png)

Here is the final codesandbox which makes use of appbase.io’s control plane to define search relevance. The only change is that we now remove the `dataField` and `includeFields` prop definitions from the FE code:



## Summary

We went over the entire process of building a search UI that’s powered by Svelte.JS and Elasticsearch. To summarize everything so far:

We start out with the use of [Searchbase](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/QuickStart/) library for scaffolding our search experience. [Searchbase](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/QuickStart/) is the underlying library used to power Searchbox libraries for React, Vue, and Vanilla JS. While we don’t have an official Svelte Searchbox library, [Searchbase](https://docs.appbase.io/docs/reactivesearch/searchbase/overview/QuickStart/) itself is battle-tested and comes with common utilities for connecting search UIs.

We then integrated different search components for creating Searchbox, Facet and Results experience for our search interface. We use the Material UI components for the interface, but other UI components could be integrated just as easily.

Finally, we style the application and show how to make it production-grade by using appbase.io’s control plane.

Links:

* **Step 1**: Base setup of Svelte.js with Searchbase (pick Svelte template from Replit)

* **Step 2**: [Configure search backend](https://github.com/appbaseio-apps/svelte-search-app/tree/main/steps/1-configure-search-backend)

* **Step 3**: [Build the search UI](https://github.com/appbaseio-apps/svelte-search-app/tree/main/steps/4-add-results-component)

* **Step 4**: [Adding styles to make responsive and professional](https://github.com/appbaseio-apps/svelte-search-app/tree/main/steps/5-apply-styling)

* **Step 5**: [Using appbase.io’s control plane to create a production grade search experience](https://github.com/appbaseio-apps/svelte-search-app/tree/main/steps/6-tuned-using-appbase-dashboard)

Searchbase itself is open-source (Apache 2.0 licensed) and available under [this Github repository](https://github.com/appbaseio/searchbox/tree/master/packages/searchbase).
