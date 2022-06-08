<script>
  import { getContext } from "svelte";
  const { searchbase } = getContext("searchContext");

  import AutoComplete from "simple-svelte-autocomplete";
  // Register search component => To render the suggestions

  const searchComponent = searchbase.register("search-component", {
    dataField: [
      "name",
      "description",
      "name.raw",
      "fullname",
      "owner",
      "topics",
    ],
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

  // keep track of the current selected suggestion
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
    // triggers api calls for dependent controllers
    searchComponent.triggerCustomQuery();
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
  placeholder="Search Git..."
  {onChange}
  hideArrow={true}
  minCharactersToSearch={0}
/>
