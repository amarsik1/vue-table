<template>
  <ul class="pagination adventFont" v-if="countPages > 1">
    <li
      :class="[
        'prev',
        'pagination-item',
        {
          disabled: activePage === 1
        }
      ]"
    >
      <button
        class="pagination-item-btn"
        href="#"
        :disabled="activePage === 1"
        @click="setActivePage(activePage - 1)"
      >
        Previous
      </button>
    </li>

    <li v-if="beforeDots" role="separator" class="pagination-item disabled">
      <button class="pagination-item-btn">…</button>
    </li>

    <li
      v-for="value of buttonsArray"
      :key="value"
      :class="[
        'pagination-item',
        {
          active: activePage === value
        }
      ]"
    >
      <button class="pagination-item-btn" @click="setActivePage(value)">
        {{ value }}
      </button>
    </li>

    <li v-if="afterDots" role="separator" class="pagination-item disabled">
      <button class="pagination-item-btn">…</button>
    </li>

    <li
      :class="[
        'pagination-item',
        'next',
        {
          disabled: activePage === countPages
        }
      ]"
    >
      <button
        class="pagination-item-btn"
        :disabled="activePage === countPages"
        @click="setActivePage(activePage + 1)"
      >
        Next
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  name: "PaginationTable",
  props: {
    onChange: {
      type: Function,
      default: () => {},
    },
    activePage: Number,
    countPages: Number,
    showDots: {
      type: Boolean,
      default: true
    },
    limit: {
      type: Number,
      default: 5
    }
  },
  computed: {
    buttonsArray: function() {
      const buttonsArray = Array(this.countPages)
        .fill(1)
        .map((v, i) => v + i);

      const minPage = this.activePage - 3;
      const from = minPage > 0 ? minPage : 0;
      const to = this.activePage + 2;

      return buttonsArray.slice(from, to);
    },
    maxPrevItems() {
      return Math.floor((this.limit - 1) / 2);
    },
    maxNextItems() {
      return Math.ceil((this.limit - 1) / 2);
    },
    beforeDots() {
      return this.showDots && this.activePage > this.maxPrevItems + 1;
    },
    afterDots() {
      return (
        this.showDots && this.activePage < this.countPages - this.maxNextItems
      );
    },
  },
  methods: {
    setActivePage: function(newValue) {
      if (this.onChange) {
        this.onChange(newValue);
      }
      this.$parent.activePage = newValue;
    }
  }
};
</script>

<style lang="scss" scoped>
.pagination {
  border-radius: 3px;
  margin-bottom: 10px;
  display: flex;
  list-style-type: none;

  @media screen and (max-width: 700px) {
    margin-top: 20px;
    justify-content: center;
  }

  &-item {
    color: #222a4f;
    cursor: pointer;

    &-btn {
      padding: 10px 14px;
      border: solid 1px #c8ced3;
      background-color: #fff;
      position: relative;
      margin-left: -1px;

      &:focus,
      &:focus-visible {
        outline: none;
      }
    }

    &.next,
    &.prev {
      @media screen and (max-width: 600px) {
        display: none;
        visibility: hidden;
      }
    }

    &.disabled &-btn {
      color: #73818f;
    }

    &:last-child &-btn {
      border-top-right-radius: 3px;
      border-bottom-right-radius: 3px;
    }

    &:first-child &-btn {
      border-top-left-radius: 3px;
      border-bottom-left-radius: 3px;
    }

    &.active &-btn {
      z-index: 2;
      border-color: #3e497a;
      background-color: #3e497a;
      color: #fff;
    }
  }
}
</style>
