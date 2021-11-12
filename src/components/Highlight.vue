<template>
  <span v-if="searchValue">
    <span>{{label.firstPart}}</span>
    <span class="highlight">{{label.founded}}</span>
    <span>{{label.lastPart}}</span>
  </span>
  <span v-else>
    {{text}}
  </span>
</template>

<script>
export default {
  name: "Hightlight",
  props: {
    text: String,
    searchValue: String,
  },
  computed: {
    label: function() {
      const firstIndex = this.text.toUpperCase().indexOf(this.searchValue.toUpperCase());
      const lastIndex = firstIndex + this.searchValue.length;

      const firstPart = this.text.slice(0, firstIndex);
      const founded = this.text.slice(firstIndex, lastIndex);
      const lastPart = this.text.slice(lastIndex);

      return {
        firstPart,
        founded,
        lastPart,
      };
    },
  },
}
</script>

<style lang="scss" scoped>
.highlight {
  background-color: yellow;
  color: black;
}
</style>