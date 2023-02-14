<template>
  <div class="search-container" ref="searchContainer">
    <!-- Search Input component -->
    <!-- @search-term: Function -->
    <!-- @input-focus: Function -->
    <SearchInput
      @search-term="handleSearchTerm"
      @input-focus="handleInputFocus"
      :isInputFocus="isInputFocus"
    />
    <h3 class="suggestion__loading" v-if="state?.loading">Loading...</h3>
    <h3 class="suggestion__error" v-if="!state?.loading && state?.error">
      {{ state.error }}
    </h3>
    <!-- @v-show: Boolean -->
    <!-- @v-if: Boolean -->
    <!-- @searchTerm: String -->
    <!-- @seggestions: Array -->
    <SearchResults
      v-show="showSuggestions"
      v-if="state?.data?.length"
      :searchTerm="searchTerm"
      :suggestions="state?.data"
    />
  </div>
</template>

<script>
import {
  defineComponent,
  ref,
  computed,
  watchEffect,
  reactive,
  onMounted,
  onBeforeUnmount,
} from "vue";
import SearchInput from "./SearchInput.vue";
import SearchResults from "./SearchResults.vue";

// const useDebounceValue = (value, delay=250) => {
//   const dbvalue = ref(value);

//   watchEffect((onAbort) => {
//       const timeout = setTimeout(() => {
//           dbvalue.value = value;
//       }, delay)

//       onAbort(() => {
//           clearTimeout(timeout);
//       })
//   })

//   return dbvalue;
// }

export default defineComponent({
  name: "Search",
  components: {
    SearchInput,
    SearchResults,
  },
  setup() {
    // reactive state
    const searchTerm = ref("");
    let state = reactive({
      loading: false,
      error: "",
      data: [],
    });

    // UX state
    const searchContainer = ref(null);
    const isInputFocus = ref(false);
    const isClickOutSide = ref(true);
    const showSuggestions = ref(false);

    const handleDocumentEvent = (e) => {
      const container = searchContainer.value;

      if (searchTerm.value.length > 1 && e.key === "Escape" || !container.contains(e.target)) {
        isInputFocus.value = false;
        isClickOutSide.value = true;
      }
      // // set remove focus from input
      // container.focus();
    };

    const handleInputFocus = (type) => {
      if (type === "focus") {
        isInputFocus.value = true;
        isClickOutSide.value = false;
      }
    };

    onMounted(() => {
      document.addEventListener("click", handleDocumentEvent);
      document.addEventListener("keydown", handleDocumentEvent);
    });

    onBeforeUnmount(() => {
      document.removeEventListener("click", handleDocumentEvent);
      document.removeEventListener("keydown", handleDocumentEvent);
    });

    // const debounceQuery = computed(() => useDebounceValue(searchTerm.value));

    // computed variables
    const securityUrl = computed(() => {
      return `http://localhost:1234/api/v1/funds?term=${searchTerm.value}`;
    });

    // update search type and reset the offset value
    const handleSearchTerm = (value) => {
      searchTerm.value = value;
    };

    watchEffect((onAbort) => {
      const abortCont = new AbortController();
      (async () => {
        // reset state data
        state.data = [];

        // if the input search query is grearter or equal than 2 characters
        if (searchTerm.value.length > 1) {
          state.loading = true;

          // ass soon as the user focus on the input
          try {
            const response = await fetch(securityUrl.value, {
              signal: abortCont.signal,
            });
            if (response.ok) {
              const json = await response.json();
              state.data = json;
            } else {
              state.error = "Error fetching data.";
            }
          } catch (error) {
            if (error instanceof Error) {
              state.error = `Error fetching data. ${error.message}`;
            } else {
              console.log("Unexpected error", error);
            }
          } finally {
            state.loading = false;
          }
        }
      })();

      onAbort(() => abortCont.abort());
    });

    watchEffect(() => {
      // Input is focused and not clicked outside cointainer
      if (isInputFocus.value && !isClickOutSide.value) {
        showSuggestions.value = true;
      } else {
        // Input is not focus and clicked outside
        showSuggestions.value = false;
      }
    });

    return {
      handleSearchTerm,
      state,
      searchTerm,
      handleInputFocus,
      showSuggestions,
      searchContainer,
      isInputFocus,
    };
  },
});

//  acceptance criteria
// Search results should appear upton entry of 2 or more characters in the search field ✓
// search string should be highlighted in auto-complete results using bold text styling ✓
// Hover/focus state in results ✓

// UX
// Search results should close and clear when 'clear' button in serch field is clicked ✓

// if underlying page is clicked or escape key is pressed ✓
//  -- the search result should close but the input to the search field should remain. ✓
//  -- If the user clicks into the search field and the results should open with the same content. ✓

// conditions
// as soon as the user starts typing, initialize a v-show, inputFocus = true, clickOuside, = false (if inputFocus && !clickOuside)
// if the input is blurred and a click outside the search container, close the hide the suggestion box
// if user hit the escape key, this should tigger a clickoutside function, close the suggestion box
// if user clicked back into the input field, then the initialize is fired again
</script>

<style scoped lang="scss">
.search-container {
  width: 700px;
  margin: 80px auto;
}

.suggestion {
  &__loading,
  &__error {
    text-align: center;
  }
}
</style>
