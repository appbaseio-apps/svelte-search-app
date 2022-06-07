<script>
    import { setContext } from 'svelte';
    import { SearchBase } from '@appbaseio/searchbase';
  import SearchBox from './lib/SearchBox.svelte';

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

<h1>GitHub Search</h1>
<SearchBox />