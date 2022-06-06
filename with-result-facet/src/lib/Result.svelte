<script>
	import { getContext, onMount } from 'svelte';
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
	// Build UI to display language options
	resultComponent.subscribeToStateChanges(
        change => {
            results = change.results.next.data;	
		},
		['results']
	);

	onMount(() => {
		resultComponent.triggerDefaultQuery();
	});
</script>

<div class="results">
    {#each results as i (i._id)}
        <div class="result">
            <div class="image">
                <img src={i.avatar} alt={i.name} />
            </div>
            <div class="details">
                <h4>{i.name}</h4>
                <p>{i.description}</p>
            </div>
        </div>
    {/each}
</div>

<style>
.results {
  padding: 10px;
  background: #fafafa;
  width: 100%;
  min-height: 50px;
}
.result {
  display: flex;
  background: white;
  margin: 10px 0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}
.result:first {
  padding-top: 0;
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

</style>
