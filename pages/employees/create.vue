<template>
  <div v-if="can('employee_access')">
    <div v-if="!preloader">
      <v-row class="mt-5 mb-10">
        <v-col cols="10">
          <h3>{{ Model }}</h3>
          <div>Dashboard / {{ Model }} / Create</div>
        </v-col>
      </v-row>
      <v-stepper v-model="e1">
        <v-stepper-header>
          <v-stepper-step :complete="e1 > 1" step="1">
            {{ Model }} Info
          </v-stepper-step>

          <v-divider></v-divider>

          <v-stepper-step :complete="e1 > 2" step="2">
            Contact Info
          </v-stepper-step>

          <v-divider></v-divider>

          <v-stepper-step step="3">
            Other Info
          </v-stepper-step>
        </v-stepper-header>

        <v-stepper-items>
          <v-stepper-content step="1">
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
                <span v-if="errors && errors.email" class="text-danger mt-2">{{
                  errors.email[0]
                }}</span>
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
                    >Confirm Password <span class="text-danger">*</span></label
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
                    >{{ !upload.name ? "Profile Picture" : "File Uploaded" }}
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
                    v-if="can('employee_create')"
                    small
                    :loading="loading"
                    color="primary"
                    @click="validate_employee"
                  >
                    Next
                  </v-btn>
                </div>
              </v-col>
            </v-row>
          </v-stepper-content>
          <v-stepper-content step="2">
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
                  <label class="col-form-label">Relative Whatsapp Number</label>
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
                  <v-btn small color="secondary" @click="e1 = 1">
                    Back
                  </v-btn>
                  <v-btn
                    v-if="can('employee_create')"
                    dark
                    small
                    :loading="loading"
                    color="primary"
                    @click="validate_contact"
                  >
                    Next
                  </v-btn>
                </div>
              </v-col>
            </v-row>
          </v-stepper-content>
          <v-stepper-content step="3">
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
                  <label class="col-form-label">Joining Date </label>
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
                    v-if="can('employee_create')"
                    dark
                    small
                    :loading="loading"
                    color="primary"
                    @click="validate_other"
                  >
                    Next
                  </v-btn>
                </div>
              </v-col>
            </v-row>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>
    </div>
    <Preloader v-else />
  </div>
  <NoAccess v-else />
</template>

<script>
export default {
  data: () => ({
    Model: "Employee",
    preloader: false,
    loading: false,
    upload: {
      name: ""
    },
    payload: {
      first_name: "",
      last_name: "",
      user_name: "",
      email: "",
      password: "",
      password_confirmation: ""
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
    designations: []
  }),
  created() {
    this.preloader = false;
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
      });
    },
    getDesignations(department_id) {
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
    onpick_attachment() {
      this.$refs.attachment_input.click();
    },

    attachment(e) {
      this.upload.name = e.target.files[0] || "";
    },
    mapper(obj){
      let payload = new FormData();

      for (let x in obj) {
         payload.append(x, obj[x]);
      }
      payload.append("profile_picture", this.upload.name);
      payload.append("company_id", this.$auth.user.company.id);

      return payload;
    },
    validate_employee() {
      this.loading = true;
      this.errors = [];

      let payload = this.mapper(this.payload);

      this.$axios
        .post("/employee/validate", payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
            return false;
          }
          this.e1 = 2;
        })
        .catch(e => console.log(e));
    },
    validate_contact() {
      this.loading = true;
      this.errors = [];

      this.$axios
        .post("employee/contact/validate", this.contact)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.e1 = 3;
          }
        })
        .catch(e => console.log(e));
    },
    validate_other() {
      this.loading = true;
      this.errors = [];

      this.$axios
        .post("employee/other/validate", this.other)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.store_data();
          }
        })
        .catch(e => console.log(e));
    },

    store_data() {
      this.loading = true;
      let final = Object.assign(this.payload,this.contact,this.other);
      let payload = this.mapper(final);

      this.$axios
        .post("/employee", payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.$router.push("/employees");
          }
        })
        .catch(e => console.log(e));
    }
  }
};
</script>
