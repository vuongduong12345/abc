<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    sort-by="calories"
    class="elevation-1 cyan lighten-3"
  >
    <template v-slot:top>
      <v-toolbar flat color="white">
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">New Item</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="editedItem.name"
                      label="Name"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="editedItem.avatar"
                      label="Avatar"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn v-if="editedIndex ===-1"
                color="blue darken-1"
                text
                @click="add"
                >Add</v-btn
              >
              <v-btn v-else
                color="blue darken-1"
                text
                @click="add"
                >Save</v-btn
              >
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>

    <template v-slot:item.action="{ item }">
      <v-icon class="red--text mr-5" small @click="editItem(item)">
        edit
      </v-icon>
      <v-icon class="orange--text" small @click="deleteItem(item)">
        delete
      </v-icon>
    </template>

    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</template>
<script>
import axios from "axios";

export default {
  data: () => ({
    dialog: false,
    headers: [
      {
        text: "Name", 
        align: "left",
        sortable: false,
        value: "name"
      },
      { text: "avatar", value: "avatar" },
      { text: "createdAt", value: "createdAt" },
      { text: "Actions", value: "action", sortable: false }
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: "",
      name: "",
      avatar: "",
      createdAt: ""
    },
    defaultItem: {
      name: "",
      avatar: "",
      createdAt: ""
    }
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    }
  },

  created() {
    this.initialize();
    this.getList();
  },

  methods: {
    initialize() {},
    getList() {
      const that = this;
      axios
        .get("http://5d6e3cc8777f67001403658b.mockapi.io/list_user")
        .then(function(response) {
          that.desserts = response["data"];
          console.log(response["data"]);
        })
        .catch(function(error) {
          console.log(error);
        })
        .finally(function() {});
    },
    editUser() {
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      const index = this.desserts.indexOf(item);
      confirm("Are you sure you want to delete this item?") &&
        this.desserts.splice(index, 1);
    },

    close() {
      this.dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },
    add() {
      
      axios({
        method: "post",
        url: "http://5d6e3cc8777f67001403658b.mockapi.io/list_user",
        data: {
          name: this.editedItem.name,
          avatar: this.editedItem.avatar
        }
      }).then(function(response) {
        console.log(response);
      });
    },
    save() {
      const data = {
        name: this.editedItem.name,
        avatar: this.editedItem.avatar
      };
      axios({
        method: "put",
        url:
          "http://5d6e3cc8777f67001403658b.mockapi.io/list_user/" +
          this.editedItem.id,
        data: data
      }).then(function(response) {
        console.log(response);
      });
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else {
        this.desserts.push(this.editedItem);
      }
      this.close();
    }
  }
};
</script>
<style scoped></style>
