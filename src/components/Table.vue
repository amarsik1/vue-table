<template>
  <div class="tableContainer">
    <div v-if="searchable || tableTitle || this.$slots.headerLeft" class="header">
      <div class="header-title-box">
        <div>
          <span class="header-title adventFont" v-if="tableTitle">{{
            tableTitle
          }}</span>
        </div>
        <div v-if="this.$slots.headerLeft" class="header-leftAddons">
          <slot name="headerLeft"></slot>
        </div>
      </div>
      <SearchTable v-if="searchable" :searchInput="searchInput" />
      <div v-if="$slots.headerRight">
        <slot name="headerRight"> </slot>
      </div>
    </div>

    <div class="tableComponent">
      <table>
        <thead>
          <tr>
            <th
              v-for="(name, index) in columnNames"
              :key="index"
              :class="[headerClass(index)]"
              :style="headerStyles(index)"
            >
              {{ name }}
            </th>
          </tr>
        </thead>

        <tbody class="position-relative">
          <tr
            v-for="(item, itemIndex) in activeRows"
            :key="itemIndex"
            @click="onClickRow ? onClickRow(item) : emptyFunc"
          >
            <template v-for="(colName, index) in rawColumnNames">
              <template v-if="$slots[colName]">
                <slot
                  :name="colName"
                  :item="item"
                  :index="itemIndex + firstItemIndex"
                  :class="cellClass(item, colName, index)"
                />
              </template>
              <td
                :class="cellClass(item, colName, index)"
                :style="headerStyles(index)"
                v-else
                :key="index"
              >
                <Highlight
                  v-if="searchable"
                  :text="String(item[colName])"
                  :searchValue="searchInput"
                />
                <span v-else>{{String(item[colName])}}</span>
              </td>
            </template>
          </tr>
          <tr v-if="!activeRows.length">
            <td :colspan="rawColumnNames.length">
              <div class="noItems">
                <i class="fas fa-ban noItemsIcon"></i>
                <span class="text adventFont">No items</span>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="tableCtrl">
      <div class="selectItemsPerPage-box">
        <SelectNumberItems
          v-if="itemsPerPageSelect"
          :itemsRowPerPage="itemsRowPerPage"
        />
      </div>
      <div class="pagination-box">
        <PaginationTable
          v-if="tablePagination && countPages > 1"
          :activePage="activePage"
          :countPages="countPages"
          :showDots="showDots"
        />
      </div>
    </div>
  </div>
</template>

<script>
import SearchTable from "./SearchTable";
import Highlight from "./Highlight";
import PaginationTable from "./PaginationTable";
import SelectNumberItems from "./SelectNumberItems";

export default {
  name: "Table",
  props: {
    tableLabels: Array,
    tableItems: Array,
    itemsPerPage: Number,
    searchableProperties: Array,
    tableTitle: String,
    titleIcons: Array,
    onClickRow: Function,
    onChange: Function,
    countItems: {
      type: Number,
      default: null
    },
    showDots: {
      type: Boolean,
      default: true
    },
    tablePagination: {
      type: Boolean,
      default: false,
    },
    searchable: {
      type: Boolean,
      default: false,
    },
    itemsPerPageSelect: {
      type: Boolean,
      default: false,
    },
  },
  components: {
    SearchTable,
    SelectNumberItems,
    PaginationTable,
    Highlight,
  },
  data() {
    return {
      initialItems: this.tableItems,
      searchInput: "",
      activePage: 1,
      rowsPerPage: this.itemsPerPage || "5",
    };
  },
  computed: {
    itemsRowPerPage: function() {
      const initItems = ["5", "10", "20", "50"];
      const currentNumberItems =
        (this.itemsPerPage && this.itemsPerPage.toString()) || "5";

      return initItems.includes(currentNumberItems)
        ? initItems
        : [currentNumberItems, ...initItems];
    },
    filteredRows: function() {
      const initItems = this.tableItems;

      const isIncluded = value => {
        const property = value.toString().toUpperCase();
        const inputValue = this.searchInput.toUpperCase();
        const isExist = property
          .toString()
          .toUpperCase()
          .includes(inputValue);

        return isExist;
      };

      if (this.searchInput.length === 0 || !this.searchable) {
        return initItems;
      }

      if (this.searchableProperties) {
        return initItems.filter(row =>
          this.searchableProperties.some(prop => {
            return isIncluded(row[prop]);
          })
        );
      }

      return initItems.filter(row => Object.values(row).some(isIncluded));
    },
    activeRows: function() {
      const showAll = this.countPages === 1
        || !this.itemsPerPageSelect
        || this.countItems

      if (showAll) {
        return this.filteredRows;
      }

      const from = this.firstItemIndex;
      const to = from + Number(this.rowsPerPage);
      const rows = this.filteredRows.slice(from, to);
      return rows;
    },
    columnNames: function() {
      if (!this.tableLabels) return [];

      return this.tableLabels.map(f => {
        return f.label !== undefined ? f.label : this.pretifyName(f.key || f);
      });
    },
    rawColumnNames: function() {
      if (this.tableLabels) {
        return this.tableLabels.map(el => el.key || el);
      }
      return this.generatedColumnNames;
    },
    firstItemIndex: function() {
      if (!this.tablePagination || this.activePage === 1) return 0;

      return (this.activePage - 1) * this.rowsPerPage || 0;
    },
    compoundProperty() {
      return [this.rowsPerPage, this.activePage].join()
    },
    countPages: function() {
      const items = this.countItems || this.filteredRows.length;
      const pages = Math.ceil(items / this.rowsPerPage);

      if (this.activePage > pages) {
        this.setActivePage(pages ? pages : 1);
      }
      return pages;
    },
  },
  methods: {
    emptyFunc: function() {},
    setActivePage: function(newValue) {
      this.activePage = newValue;
    },
    pretifyName(name) {
      return name
        .replace(/[-_.]/g, " ")
        .replace(/ +/g, " ")
        .replace(/([a-z0-9])([A-Z])/g, "$1 $2")
        .split(" ")
        .map(word => word.charAt(0).toUpperCase() + word.slice(1))
        .join(" ");
    },
    headerStyles(index) {
      let style = "vertical-align:middle;overflow:hidden;";
      if (
        this.tableLabels &&
        this.tableLabels[index] &&
        this.tableLabels[index]._style
      ) {
        style += this.tableLabels[index]._style;
      }
      return style;
    },
    cellClass(item, colName, index) {
      let classes = [];
      if (item._cellClasses && item._cellClasses[colName]) {
        classes.push(item._cellClasses[colName]);
      }
      if (this.tableLabels && this.tableLabels[index]._classes) {
        classes.push(this.tableLabels[index]._classes);
      }
      return classes;
    },
    headerClass(index) {
      const fields = this.tableLabels;
      return fields && fields[index]._classes ? fields[index]._classes : "";
    },
    setCountPages: function() {
      const items = this.countItems || this.filteredRows.length;
      const pages = Math.ceil(items / this.rowsPerPage);

      if (this.activePage > pages) {
        this.activePage = pages ? pages : 1;
      }

      if (this.countPages !== pages) {
        this.countPages = pages;
      }
    },
    handleChangeData: function() {
      this.onChange(this.rowsPerPage, this.activePage);
    },
  },
  mounted() {
    if (this.tablePagination) {
      this.setCountPages();
    }
  },
  updated() {
    if (this.tablePagination) {
      this.setCountPages();
    }
  },
  watch: {
    compoundProperty: function() {
      if (this.onChange) {
        this.handleChangeData();
      }
    },
  }
};
</script>

<style lang="scss" scoped>
.tableContainer {
  width: 100%;
}

.noItemsIcon {
  color: red;
}

.header {
  display: flex;
  flex-wrap: wrap;
  margin-bottom: 20px;
  justify-content: space-between;

  @media screen and (max-width: 700px) {
    flex-direction: column;
  }

  &-leftAddons {
    margin-left: 12px;
    display: flex;

    @media (max-width: 450px) {
      margin-left: 0;
    }
  }

  &-title-box {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
  }

  &-title {
    font-size: 25px;
    font-weight: 500;
  }
}

.tableComponent {
  overflow-x: auto;
  border-top-left-radius: 4px;
  border-top-right-radius: 4px;
  border: solid 1px #c8ced3;

  .noItems {
    margin: 20px 0;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;

    @media (max-width: 768px) {
      justify-content: flex-start;
    }

    .text {
      margin-left: 20px;
      display: flex;
      align-items: center;
      font-size: 25px;
    }
  }

  table {
    width: 100%;

    tbody,
    thead,
    tr {
      width: 100%;
    }

    thead {
      font-size: 16px;
      border-bottom: solid 1px #c8ced3;
    }

    tbody {
      tr {
        &:hover {
          background-color: rgba(0, 0, 27, 0.075);
          cursor: pointer;
        }
      }
    }

    tr {
      border-bottom: solid 1px #c8ced3;

      &:last-child {
        border-bottom: none;
      }

      &:nth-child(2n) {
        background-color: #f4f4f4;
      }
    }

    th,
    td {
      font-size: 16px;
      padding: 10px;
    }
  }
  .textCenter {
    text-align: center;
  }
}

.tableCtrl {
  margin-top: 30px;
  display: flex;
  justify-content: space-between;

  @media screen and (max-width: 700px) {
    flex-direction: column;
    align-content: flex-start;
  }
}
</style>
