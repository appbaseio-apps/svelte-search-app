<script>
  import { setContext } from "svelte";
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

<h1>GitHub Search</h1>
