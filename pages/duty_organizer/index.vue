<template>
  <div v-if="can(`assign_permission_access`)">
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <v-row class="mt-5 mb-5">
      <v-col cols="6">
        <h3>{{ Module }}</h3>
        <div>Dashboard / {{ Module }}</div>
      </v-col>
      <v-col cols="6">
        <div class="text-right">
          <v-btn
            v-if="can(`assign_permission_delete`)"
            small
            color="error"
            class="mr-2 mb-2"
            @click="delteteSelectedRecords"
            >Delete Selected Records</v-btn
          >

          <v-btn
            v-if="can(`assign_permission_create`)"
            small
            color="primary"
            to="/duty_organizer/create"
            class="mb-2"
            >{{ Module }} +</v-btn
          >
        </div>
      </v-col>
    </v-row>
    <v-data-table
      v-if="can(`assign_permission_view`)"
      v-model="ids"
      show-select
      item-key="id"
      :headers="headers"
      :items="data"
      :server-items-length="total"
      :loading="loading"
      :options.sync="options"
      :footer-props="{
        itemsPerPageOptions: [5, 10, 15]
      }"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar dark class="gradient-custom-2">{{ Module }}s</v-toolbar>

        <v-toolbar flat color="">
          <v-toolbar-title>List</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>

          <v-text-field
            @input="searchIt"
            v-model="search"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-toolbar>
      </template>
      <template v-slot:item.permission_names="{ item }">
        <v-chip
          class="ma-1"
          small
          color="primary"
          v-for="(pa, idx) in item.permission_names"
          :key="idx"
        >
          {{ pa }}
        </v-chip>

        <v-chip class="ma-1" small v-if="item.permission_names.length == 0">
          No permissions assigned
        </v-chip>
      </template>
      <template v-slot:item.action="{ item }">
        <v-icon
          v-if="can(`assign_permission_delete`)"
          color="error"
          small
          @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <!-- <v-btn color="primary" @click="initialize">Reset</v-btn> -->
      </template>
    </v-data-table>
    <NoAccess v-else />
  </div>
  <NoAccess v-else />
</template>
<script>
export default {
  data: () => ({
    Module: "Duty Organizer",
    options: {},
    endpoint: "no-shift",
    search: "",
    snackbar: false,
    dialog: false,
    ids: [],
    loading: false,
    total: 0,
    headers: [],

    response: "",
    data: [],
    errors: []
  }),

  computed: {},

  watch: {
    dialog(val) {
      val || this.close();
      this.errors = [];
      this.search = "";
    },
    options: {
      handler() {
        this.getDataFromApi();
        this.getHeaders();
      },
      deep: true
    }
  },
  created() {
    this.loading = true;
  },

  methods: {
    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },
    getHeaders() {
      this.headers = [
        {
          text: "Employee Id",
          align: "left",
          sortable: false,
          value: "employees.employee_id"
        },
        {
          text: "Employee Full Name",
          align: "left",
          sortable: false,
          value: "employees.first_name"
        },
        {
          text: "Phone Number",
          align: "left",
          sortable: false,
          value: "employees.phone_number"
        },
        {
          text: "Whatsapp Number",
          align: "left",
          sortable: false,
          value: "employees.whatsapp_number"
        },

        { text: "Actions", align: "center", value: "action", sortable: false }
      ];
    },

    getDataFromApi(url = this.endpoint) {
      this.loading = true;

      const { page, itemsPerPage } = this.options;

      let options = {
        params: {
          per_page: itemsPerPage,
          company_id: this.$auth.user.company.id
        }
      };

      this.$axios.get(`${url}?page=${page}`, options).then(({ data }) => {
        this.data = data.data;
        this.total = data.total;
        this.loading = false;
      });
    },
    searchIt(e) {
      if (e.length == 0) {
        this.getDataFromApi();
      } else if (e.length > 2) {
        this.getDataFromApi(`${this.endpoint}/search/${e}`);
      }
    },

    delteteSelectedRecords() {
      let just_ids = this.ids.map(e => e.id);
      confirm(
        "Are you sure you wish to delete selected records , to mitigate any inconvenience in future."
      ) &&
        this.$axios
          .post(`${this.endpoint}/delete/selected`, {
            ids: just_ids
          })
          .then(res => {
            if (!res.data.status) {
              this.errors = res.data.errors;
            } else {
              this.getDataFromApi();
              this.snackbar = res.data.status;
              this.ids = [];
              this.response = "Selected records has been deleted";
            }
          })
          .catch(err => console.log(err));
    },

    deleteItem(item) {
      confirm(
        "Are you sure you wish to delete , to mitigate any inconvenience in future."
      ) &&
        this.$axios
          .delete(this.endpoint + "/" + item.id)
          .then(({ data }) => {
            const index = this.data.indexOf(item);
            this.data.splice(index, 1);
            this.snackbar = data.status;
            this.response = data.message;
          })
          .catch(err => console.log(err));
    }
  }
};
</script>
