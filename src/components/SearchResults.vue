<template>
  <div class="suggestions">
    <ul class="suggestions__list">
      <li class="suggestions__list-item suggestions__list-item--header">
        <div class="suggestions__name">
          <p>Name</p>
        </div>
        <div class="suggestions__ticker">
          <p>Ticker</p>
        </div>
        <div class="suggestions__exchange">
          <p>Exchange</p>
        </div>
      </li>
      <li
        v-for="item in suggestions"
        :key="item.id"
        class="suggestions__list-item"
      >
        <div class="suggestions__name">
          <p v-html="highlight(item.name, searchTerm)"></p>
        </div>
        <div class="suggestions__ticker">
          <p v-html="highlight(item.ticker, searchTerm)"></p>
        </div>
        <div class="suggestions__exchange">
          <p>{{ item.exchange }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "SearchInput",

  props: {
    suggestions: {
      type: Array,
    },
    searchTerm: {
      type: String,
    },
  },
  setup() {
    const highlight = (items, term) => {
      return items.replace(
        new RegExp(term, "gi"),
        (match) => `<b>${match}</b>`
      );
    };
    return {
      highlight,
    };
  },
});
</script>

<style lang="scss" scoped>
.suggestions {
  background-color: var(--color-white);
  border-radius: 9px;
  box-shadow: 0 4px 26px 5px rgba(0, 0, 0, 0.1);
  height: 400px;
  margin-block-start: 16px;
  overflow-y: scroll;
  padding-inline: 8px;
  width: 650px;

  &__name {
    flex: 4;
  }
  &__ticker,
  &__exchange {
    flex: 1;
  }

  &__list {
    font-size: 14px;
    list-style: none;
    margin: 0;
    padding: 0;

    :hover {
      background-color: var(--hover-color);
      cursor: pointer;
    }

    &-item {
      border-bottom: 1px solid lightgray;
      display: flex;
      margin-inline: 32px;
      position: relative;
      color: var(--primary-color);

      &--header {
        font-weight: 600;
        color: var(--seconndary-color);
      }

      &.suggestions__list-item--header {
        background-color: var(--color-white);
        position: sticky;
        top: 0;
        z-index: 100;

        &:hover,
        *:hover {
          background-color: var(--color-white);
          cursor: default;
        }
      }
    }
  }
}
</style>
