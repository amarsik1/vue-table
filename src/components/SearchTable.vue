<template>
  <label class="searchBox">
    <span class="searchBox-title adventFont">Search:</span>
    <input
      class="searchBox-input adventFont"
      type="text"
      placeholder="Search"
      @input="setInputValue"
      :value="searchInput"
    />
  </label>
</template>

<script>
export default {
  name: "SearchTable",
  props: {
    searchInput: String
  },
  methods: {
    setInputValue: function({ target: { value } }) {
      this.$parent.searchInput = value;
      this.$parent.setCountPages();
    }
  }
};
</script>

<style lang="scss" scoped>
.searchBox {
  margin: 0;
  display: flex;
  justify-content: flex-end;
  align-items: center;


  @media (max-width: 700px) {
    margin-top: 10px;
  }
  
  &-title {
    margin-right: 20px;

    @media (max-width: 400px) {
      display: none;
    }
  }

  &-input {
    max-width: 300px;
    padding-left: 10px;
    width: 100%;
    height: 28px;
    border: 1px solid #c8ced3;
    border-radius: 3px;

    @media (max-width: 400px) {
      max-width: none;
    }

    &::placeholder {
      opacity: 0;
      color: gray;

      @media (max-width: 400px) {
        opacity: 1;
      }
    }

    &:focus-visible {
      outline: none;
    }
  }
}
</style>
