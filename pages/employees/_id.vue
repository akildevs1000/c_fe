<template>
  <div v-if="can('employee_access')">
    <div v-if="!preloader">
      <div class="text-center ma-2">
        <v-snackbar
          v-model="snackbar"
          top="top"
          color="secondary"
          elevation="24"
        >
          {{ response }}
        </v-snackbar>
      </div>
      <v-row class="mt-5 mb-10">
        <v-col cols="10">
          <h3>{{ Model }}</h3>
          <div>Dashboard / {{ Model }} / Edit</div>
        </v-col>
        <v-col cols="2">
          <div class="display-1 pa-2 text-right">
            <v-btn small class="primary" :to="`/employees`">
              <v-icon small>mdi-arrow-left</v-icon>&nbsp;Back
            </v-btn>
          </div>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-card>
            <v-tabs color="primary">
              <v-tab>
                <v-icon left>
                  mdi-account
                </v-icon>
              </v-tab>
              <v-tab>
                <v-icon left>
                  mdi-phone
                </v-icon>
              </v-tab>
              <v-tab>
                <v-icon left>
                  mdi-chart-bubble
                </v-icon>
              </v-tab>

              <v-tab-item>
                <v-card flat>
                  <v-card-text>
                    <div class="row">
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"></label>
                          First Name <span class="text-danger">*</span>
                          <input
                            v-model="payload.first_name"
                            class="form-control"
                            type=""
                          />
                        </div>
                        <span
                          v-if="errors && errors.first_name"
                          class="text-danger mt-2"
                          >{{ errors.first_name[0] }}</span
                        >
                      </div>

                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"></label>
                          Last Name <span class="text-danger">*</span>
                          <input
                            v-model="payload.last_name"
                            class="form-control"
                            type=""
                          />
                        </div>
                        <span
                          v-if="errors && errors.last_name"
                          class="text-danger mt-2"
                          >{{ errors.last_name[0] }}</span
                        >
                      </div>

                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"></label>
                          User Name <span class="text-danger">*</span>
                          <input
                            v-model="payload.user_name"
                            class="form-control"
                            type=""
                          />
                        </div>
                        <span
                          v-if="errors && errors.user_name"
                          class="text-danger mt-2"
                          >{{ errors.user_name[0] }}</span
                        >
                      </div>

                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"></label>
                          Email <span class="text-danger">*</span>
                          <input
                            v-model="payload.email"
                            class="form-control"
                            type="email"
                          />
                        </div>
                        <span
                          v-if="errors && errors.email"
                          class="text-danger mt-2"
                          >{{ errors.email[0] }}</span
                        >
                      </div>
                    </div>
                    <div class="row">
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"
                            >Password <span class="text-danger">*</span></label
                          >
                          <input
                            v-model="payload.password"
                            class="form-control"
                            type="password"
                          />
                          <span
                            v-if="errors && errors.password"
                            class="text-danger mt-2"
                            >{{ errors.password[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"
                            >Confirm Password
                            <span class="text-danger">*</span></label
                          >
                          <input
                            v-model="payload.password_confirmation"
                            class="form-control"
                            type="password"
                          />
                          <span
                            v-if="errors && errors.password_confirmation"
                            class="text-danger mt-2"
                            >{{ errors.password_confirmation[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Profile Picture</label>
                          <br />
                          <v-btn
                            text
                            small
                            class="form-control primary"
                            @click="onpick_attachment"
                            >{{
                              !upload.name ? "Profile Picture" : "File Uploaded"
                            }}
                            <v-icon right dark>mdi-cloud-upload</v-icon></v-btn
                          >

                          <input
                            required
                            type="file"
                            @change="attachment"
                            style="display: none"
                            accept="image/*"
                            ref="attachment_input"
                          />

                          <span
                            v-if="errors && errors.profile_picture"
                            class="text-danger mt-2"
                            >{{ errors.profile_picture[0] }}</span
                          >
                        </div>
                      </div>
                    </div>

                    <v-row>
                      <v-col cols="12">
                        <div class="text-right">
                          <v-btn
                            v-if="can('employee_edit')"
                            small
                            :loading="loading"
                            color="primary"
                            @click="update_employee"
                          >
                            Submit
                          </v-btn>
                        </div>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-tab-item>
              <v-tab-item>
                <v-card flat>
                  <v-card-text>
                    <div class="row">
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Phone Number</label>
                          <span class="text-danger">*</span>
                          <input
                            v-model="contact.phone_number"
                            class="form-control"
                            type="number"
                          />
                          <span
                            v-if="errors && errors.phone_number"
                            class="text-danger mt-2"
                            >{{ errors.phone_number[0] }}</span
                          >
                        </div>
                      </div>

                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Whatsapp Number</label>
                          <span class="text-danger">*</span>
                          <input
                            v-model="contact.whatsapp_number"
                            class="form-control"
                            type="number"
                          />
                          <span
                            v-if="errors && errors.whatsapp_number"
                            class="text-danger mt-2"
                            >{{ errors.whatsapp_number[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Relative Number</label>
                          <input
                            v-model="contact.phone_relative_number"
                            class="form-control"
                            type="number"
                          />
                          <span
                            v-if="errors && errors.phone_relative_number"
                            class="text-danger mt-2"
                            >{{ errors.phone_relative_number[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label"
                            >Relative Whatsapp Number</label
                          >
                          <input
                            v-model="contact.whatsapp_relative_number"
                            class="form-control"
                            type="number"
                          />
                          <span
                            v-if="errors && errors.whatsapp_relative_number"
                            class="text-danger mt-2"
                            >{{ errors.whatsapp_relative_number[0] }}</span
                          >
                        </div>
                      </div>
                    </div>

                    <v-row>
                      <v-col cols="12">
                        <div class="text-right">
                          <v-btn
                            v-if="can('employee_edit')"
                            small
                            :loading="loading"
                            color="primary"
                            @click="update_contact"
                          >
                            Submit
                          </v-btn>
                        </div>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-tab-item>
              <v-tab-item>
                <v-card flat>
                  <v-card-text>
                    <div class="row">
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Employee Id</label>
                          <span class="text-danger">*</span>
                          <input
                            v-model="other.employee_id"
                            class="form-control"
                            type=""
                          />
                          <span
                            v-if="errors && errors.employee_id"
                            class="text-danger mt-2"
                            >{{ errors.employee_id[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Joining Date</label>
                          <span class="text-danger">*</span>
                          <input
                            v-model="other.joining_date"
                            class="form-control"
                            type="date"
                          />
                          <span
                            v-if="errors && errors.joining_date"
                            class="text-danger mt-2"
                            >{{ errors.joining_date[0] }}</span
                          >
                        </div>
                      </div>

                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Department</label>
                          <span class="text-danger">*</span>
                          <select
                            @change="getDesignations(other.department_id)"
                            v-model="other.department_id"
                            class="form-select"
                            aria-label="Default select"
                          >
                            <option value="">Select Department</option>
                            <option
                              v-for="(item, idx) in departments"
                              :key="idx"
                              :value="item.id"
                              >{{ item.name }}</option
                            >
                          </select>
                          <span
                            v-if="errors && errors.department_id"
                            class="text-danger mt-2"
                            >{{ errors.department_id[0] }}</span
                          >
                        </div>
                      </div>
                      <div class="col-sm-6">
                        <div class="form-group">
                          <label class="col-form-label">Designation</label>
                          <span class="text-danger">*</span>
                          <select
                            v-model="other.designation_id"
                            class="form-select"
                            aria-label="Default select"
                          >
                            <option value="">Select Designation</option>
                            <option
                              v-for="(item, idx) in designations"
                              :key="idx"
                              :value="item.id"
                              >{{ item.name }}</option
                            >
                          </select>
                          <span
                            v-if="errors && errors.designation_id"
                            class="text-danger mt-2"
                            >{{ errors.designation_id[0] }}</span
                          >
                        </div>
                      </div>
                    </div>
                    <v-row>
                      <v-col cols="12">
                        <div class="text-right">
                          <v-btn
                            v-if="can('employee_edit')"
                            small
                            :loading="loading"
                            color="primary"
                            @click="update_other"
                          >
                            Submit
                          </v-btn>
                        </div>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-tab-item>
            </v-tabs>
          </v-card>
        </v-col>
      </v-row>
    </div>
    <Preloader v-else />
  </div>
  <NoAccess v-else />
</template>

<script>
export default {
  data: () => ({
    Model: "Employee",
    id: "",
    loading: false,
    preloader: false,
    upload: {
      name: ""
    },

    payload: {
      first_name: "",
      last_name: "",
      user_name: "",
      email: ""
    },
    contact: {
      phone_number: "",
      whatsapp_number: "",
      phone_relative_number: "",
      whatsapp_relative_number: ""
    },
    other: {
      designation_id: "",
      department_id: "",
      joining_date: "",
      employee_id: ""
    },
    e1: 1,
    errors: [],
    departments: [],
    designations: [],

    data: {},
    response: "",
    snackbar: false
  }),
  async created() {
    this.getDataFromApi();
    this.getDepartments();
  },
  methods: {
    getDepartments() {
      let options = {
        params: {
          per_page: 100,
          company_id: this.$auth.user.company.id
        }
      };
      this.$axios.get(`departments`, options).then(({ data }) => {
        this.departments = data.data;
        this.getDesignations();
      });
    },
    getDesignations(department_id = this.other.department_id) {
      let options = {
        params: {
          per_page: 100,
          department_id: department_id,
          company_id: this.$auth.user.company.id
        }
      };
      this.$axios
        .get(`designations-by-department`, options)
        .then(({ data }) => {
          this.designations = data.data;
        });
    },
    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },
    getDataFromApi() {
      this.preloader = true;
      this.id = this.$route.params.id;
      this.$axios.get(`employee/${this.id}`).then(({ data }) => {

        // console.log("🚀 ~ file: _id.vue ~ line 515 ~ this.$axios.get ~ data", data)
        // return

        this.payload = {
          first_name: data.first_name,
          last_name: data.last_name,
          user_name: data.user.name,
          email: data.user.email
        };

        this.contact = {
          phone_number: data.phone_number,
          whatsapp_number: data.whatsapp_number,
          phone_relative_number: data.phone_relative_number,
          whatsapp_relative_number: data.whatsapp_relative_number
        };

        this.other = {
          designation_id: data.designation_id,
          department_id: data.department_id,
          joining_date: data.edit_joining_date,
          employee_id: data.employee_id
        };
        this.preloader = false;
      });
    },

    onpick_attachment() {
      this.$refs.attachment_input.click();
    },

    attachment(e) {
      this.upload.name = e.target.files[0] || "";
    },

    mapper(obj) {
      let payload = new FormData();

      for (let x in obj) {
        payload.append(x, obj[x]);
      }

      return payload;
    },

    update_employee() {
      let payload = this.mapper(this.payload);

      if (this.upload.name) {
        payload.append("profile_picture", this.upload.name);
      }

      payload.append("password", this.payload.password);
      payload.append(
        "password_confirmation",
        this.payload.password_confirmation
      );

      this.start_process(`/employee/${this.id}/update`, payload, `Employee`);
    },
    update_contact() {
      this.start_process(
        `/employee/${this.id}/update/contact`,
        this.contact,
        `Contact`
      );
    },
    update_other() {
      this.start_process(
        `/employee/${this.id}/update/other`,
        this.other,
        `Other details`
      );
    },
    start_process(url, payload, model) {
      this.loading = true;

      this.$axios
        .post(url, payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.snackbar = true;

            this.response = model + " updated successfully";
          }
        })
        .catch(e => console.log(e));
    }
  }
};
</script>
