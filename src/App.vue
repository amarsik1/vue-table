<template>
  <div>
    <div class="controls">
      <h4>Component props:</h4>
      <label for="searchable"
        >Searchable:
        <input name="searchable" type="checkbox" v-model="searchable" />
      </label>

      <label for="itemsPerPageSelect"
        >Select items per page?:
        <input
          name="itemsPerPageSelect"
          type="checkbox"
          v-model="itemsPerPageSelect"
        />
      </label>

      <label v-if="itemsPerPageSelect" for="tablePagination"
        >Pagination:
        <input
          name="tablePagination"
          type="checkbox"
          v-model="tablePagination"
        />
      </label>
      
      <label for="tableTitle"
        >Table title:
        <input
          name="tableTitle"
          type="text"
          v-model="tableTitle"
        />
      </label>
    </div>

    <Table
      class="table"
      :tableItems="tableItems"
      :tableLabels="tableLabels"
      :tableTitle="tableTitle"
      tableIcons="fas fa-ban"
      :searchable="searchable"
      :itemsPerPageSelect="itemsPerPageSelect"
      :tablePagination="tablePagination"
    >
      <template v-slot:activity="{ item }">
        <td>
          <button
            @click="
              toEditUser = { ...item };
              editedUser = { ...item };
            "
          >
            <i class="far fa-edit"></i>
          </button>
          <button @click="userToDelete = item">
            <i class="far fa-trash-alt"></i>
          </button>
        </td>
      </template>
    </Table>

    <transition>
      <ConfirmationModal
        v-if="userToDelete"
        :title="`Are you sure that you want to delete: '${userToDelete.firstName} ${userToDelete.lastName}'`"
        :confirmFunc="() => deleteItem(userToDelete.id)"
        :cancelFunc="() => (userToDelete = null)"
      />
    </transition>

    <transition>
      <ConfirmationModal
        v-if="editedUser"
        title="Edit user"
        :confirmFunc="() => saveUser()"
        :cancelFunc="() => editedUser = null"
      >
        <div class="editForm">
          <label>
            <p>First name:</p>
            <input type="text" v-model="editedUser.firstName" />
          </label>

          <label>
            <p>Last name:</p>
            <input type="text" v-model="editedUser.lastName" />
          </label>

          <label>
            <p>Email:</p>
            <input type="text" v-model="editedUser.email" />
          </label>
        </div> 
      </ConfirmationModal>
    </transition>
  </div>
</template>

<script>
import ConfirmationModal from "./components/ConfirmationModal";
import users from "./users.json";
import Table from "./components/Table";

export default {
  name: "App",
  components: {
    Table,
    ConfirmationModal,
  },
  data() {
    return {
      tableItems: users,
      searchable: false,
      itemsPerPageSelect: true,
      tableTitle: 'Users',
      tablePagination: false,
      userToDelete: null,
      editedUser: null,
      tableLabels: [
        {
          key: "email",
          _style: "width: 33%; min-width: 300px",
        },
        {
          key: "firstName",
          label: "First name",
          _style: "width: 20%; min-width: 100px",
        },
        {
          key: "lastName",
          label: "Last name",
          _style: "width: 20%; min-width: 100px",
        },
        {
          key: "activity",
          _style: "width: 10%; min-width: 100px",
        },
      ],
    };
  },
  methods: {
    deleteItem: function (id) {
      const result = this.tableItems.filter((i) => i.id !== id);
      this.tableItems = result;
    },
    saveUser: function () {
      console.log("asdasd");
      if (!this.editedUser) return;
      console.log(this.editedUser);
      const result = this.tableItems.map((user) =>
        user.id === this.editedUser.id ? this.editedUser : user
      );
      console.log(result);
      this.tableItems = result;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.editForm {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  
  label {
    margin-bottom: 10px;

    p {
      display: inline-block;
      text-align: left;
      margin: 0;
      min-width: 80px;
    }
  }
}

.table {
  margin-top: 20px;
  padding: 30px;
  background-color: #fff;
  border-radius: 10px;
  box-sizing: border-box;
  box-shadow: 0 0 20px -14px #222a4f;
}

.controls {
  margin-left: 20px;
  display: flex;
  flex-direction: column;

  h4 {
    text-align:left;
  }

  label {
    max-width: 300px;
    display: flex;
    justify-content: space-between;
  }
}
</style>
