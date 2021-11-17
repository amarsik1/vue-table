<template>
  <span v-if="searchValue && searchable && label">
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
    searchable: Boolean,
    searchValue: String,
  },
  computed: {
    label: function() {
      const firstIndex = this.text.toUpperCase().indexOf(this.searchValue.toUpperCase());
      if (firstIndex === -1) return;

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