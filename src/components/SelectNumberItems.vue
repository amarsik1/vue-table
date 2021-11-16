<template>
  <div class="selectItemsPerPage">
    <label class="mfe-2 form-inline">
      <span class="adventFont">Show</span>
      <select
        :value="$parent.rowsPerPage"
        class="select"
        @change="setRowsPerPage"
      >
        <option
          v-for="num in itemsRowPerPage"
          :value="num"
          :key="num"
        >
          {{num}}
        </option>
      </select> 
      <span class="adventFont">entries</span>
    </label>
  </div>
</template>

<script>
export default {
  name: "SelectNumberItems",
  props: {
    itemsRowPerPage: Array
  },
  methods: {
    setRowsPerPage: function({ target: { value } }) {
      this.$parent.rowsPerPage = value;
      this.$parent.setCountPages();
    }
  }
};
</script>

<style lang="scss" scoped>
.selectItemsPerPage {

  .select {
    margin: 0 5px;
  }
}
</style>
