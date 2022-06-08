<script>
  import { getContext, onMount } from "svelte";
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
</script>

<AutoComplete
  searchFunction={getSuggestions}
  delay={500}
  localFiltering={false}
  showLoadingIndicator={true}
  labelFieldName="value"
  valueFieldName="value"
  bind:selectedItem
  placeholder="Search GitHub"
  {onChange}
  hideArrow={true}
  minCharactersToSearch={0}
/>
