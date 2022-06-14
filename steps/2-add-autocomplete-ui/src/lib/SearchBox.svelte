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

  let selectedItem;
  let inputValue = "";
  async function getSuggestions(keyword) {
    // Set the value to fetch the suggestions
    searchComponent.setValue(keyword, { triggerDefaultQuery: false });
    const results =
      (await searchComponent.triggerDefaultQuery())?.hits?.hits ?? [];
    if (inputValue) {
      results.unshift({
        label: `Find all results for \"${inputValue}\"`,
        value: inputValue,
      });
    }
    return results;
  }

  function onChange(item) {
    selectedItem = item;
    searchComponent.setValue(item?.value, {
      triggerDefaultQuery: true,
      triggerCustomQuery: true,
    });
  }

  onMount(() => {
    const listenInputValueChange = (e) => {
      if (!e.target.value) {
        // reset search-component controller's value
        searchComponent.setValue("", {
          triggerDefaultQuery: true,
          triggerCustomQuery: true,
        });
      }

      inputValue = e.target.value;
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
