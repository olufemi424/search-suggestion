<template>
  <div class="search">
    <div class="search__container">
      <input
        v-model="query"
        @input="handleInputChange"
        @focus="handleInputFocus($event, 'focus')"
        class="search__input"
        type="text"
        placeholder="Search for securities"
      />
      <button class="search__btn search__btn--glass">
        <span class="material-symbols-outlined">search</span>
      </button>
      <button
        v-show="query.length"
        @click="clearInput"
        class="search__btn search__btn--close"
      >
        <span class="material-symbols-outlined">close</span>
      </button>
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";

const debounce = (fn, delay) => {
  let timeout;
  return (...args) => {
    if (timeout) {
      clearTimeout(timeout);
    }

    timeout = setTimeout(() => {
      fn(...args);
    }, delay);
  };
};

export default defineComponent({
  name: "SearchInput",
  components: {},
  setup(_, { emit }) {
    const query = ref("");

    const handleInputChange = debounce(() => {
      emit("search-term", query.value);
    }, 250);

    const clearInput = debounce(() => {
      query.value = "";
      emit("search-term", "");
    }, 250);

    const handleInputFocus = (e, type) => {
      e.preventDefault();
      emit("input-focus", type);
    };

    return {
      query,
      handleInputChange,
      clearInput,
      handleInputFocus,
    };
  },
});
</script>

<style lang="scss" scoped>
.search {
  display: flex;
  justify-content: center;
  padding-inline: 8px;
  width: 650px;

  &__container {
    position: relative;
  }

  &__input {
    background-color: rgba(211, 211, 211, 0.636);
    border-radius: 9px;
    border: none;
    margin: 0 auto;
    padding: 12px;
    text-indent: 28px;
    width: 400px;
  }

  .search__btn {
    background: transparent;
    border: none;
    position: absolute;
    top: 6px;

    &--close {
      border-radius: 50%;
      cursor: pointer;
      right: 1px;
    }

    &--glass {
      left: 2px;
    }

    .material-symbols-outlined {
      color: var(--icon-color);

      &:hover,
      &:focus {
        background-color: var(--hover-color);
        border-radius: 50%;
      }
    }
  }
}
</style>
