<template>
  <div v-if="can('employee_access')">
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <div v-if="!loading">
      <v-row class="mt-10 mb-10">
        <v-col cols="10">
          <h3>Employee</h3>
          <div>Dashboard / Employee / Details</div>
        </v-col>
      </v-row>
      <v-dialog v-model="passport_info" max-width="500px">
        <v-card>
          <v-card-actions>
            <span class="headline">Passport Info </span>
          </v-card-actions>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("passport no")
                    }}</label>
                    <input
                      v-model="editedItem.passport_no"
                      class="form-control"
                      type="number"
                    />
                    <span
                      v-if="errors && errors.passport_no"
                      class="text-danger mt-2"
                      >{{ errors.passport_no[0] }}</span
                    >
                  </div>
                </v-col>
                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("passport expiry")
                    }}</label>

                    <input
                      v-model="editedItem.passport_expiry"
                      class="form-control"
                      type="date"
                    />
                    <span
                      v-if="errors && errors.passport_expiry"
                      class="text-danger mt-2"
                      >{{ errors.passport_expiry[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{ caps("tel") }}</label>
                    <input
                      v-model="editedItem.tel"
                      class="form-control"
                      type="tel"
                    />
                    <span
                      v-if="errors && errors.tel"
                      class="text-danger mt-2"
                      >{{ errors.tel[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("nationality")
                    }}</label>
                    <input
                      v-model="editedItem.nationality"
                      class="form-control"
                      type=""
                    />
                    <span
                      v-if="errors && errors.nationality"
                      class="text-danger mt-2"
                      >{{ errors.nationality[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{ caps("religion") }}</label>
                    <input
                      v-model="editedItem.religion"
                      class="form-control"
                      type=""
                    />
                    <span
                      v-if="errors && errors.religion"
                      class="text-danger mt-2"
                      >{{ errors.religion[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("marital status")
                    }}</label>
                    <input
                      v-model="editedItem.marital_status"
                      class="form-control"
                      type=""
                    />
                    <span
                      v-if="errors && errors.marital_status"
                      class="text-danger mt-2"
                      >{{ errors.marital_status[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("no of spouse")
                    }}</label>
                    <input
                      min="0"
                      v-model="editedItem.no_of_spouse"
                      class="form-control"
                      type="number"
                    />
                    <span
                      v-if="errors && errors.no_of_spouse"
                      class="text-danger mt-2"
                      >{{ errors.no_of_spouse[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("no of children")
                    }}</label>
                    <input
                      min="0"
                      v-model="editedItem.no_of_children"
                      class="form-control"
                      type="number"
                    />
                    <span
                      v-if="errors && errors.no_of_children"
                      class="text-danger mt-2"
                      >{{ errors.no_of_children[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="12">
                  <a
                    href="javascrip:void(0)"
                    @click="add_other_personal_info = !add_other_personal_info"
                    >{{
                      caps(
                        `${
                          add_other_personal_info ? "hide" : "show"
                        } other field`
                      )
                    }}</a
                  >
                </v-col>

                <v-col cols="6" v-if="add_other_personal_info">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("other text")
                    }}</label>
                    <input
                      v-model="editedItem.other_text"
                      class="form-control"
                    />
                    <span
                      v-if="errors && errors.other_text"
                      class="text-danger"
                      >{{ errors.other_text[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6" v-if="add_other_personal_info">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("other value")
                    }}</label>
                    <input
                      v-model="editedItem.other_value"
                      class="form-control"
                    />
                    <span
                      v-if="errors && errors.other_value"
                      class="text-danger mt-2"
                      >{{ errors.other_value[0] }}</span
                    >
                  </div>
                </v-col>

                <span v-if="errors && errors.length" class="error--text">{{
                  errors
                }}</span>
              </v-row>

              <!-- <v-row v-for="(item, index) in editedItem.items" :key="index">
                <v-col cols="5">
                  <v-text-field v-model="item.key" label="label"></v-text-field>
                </v-col>
                <v-col cols="5">
                  <v-text-field
                    v-model="item.value"
                    label="value"
                  ></v-text-field>
                </v-col>
                <v-col cols="2" class="mt-5">
                  <v-btn dark class="error" fab @click="removeItem(index)" x-small>
                    <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </v-col>
                <span v-if="errors && errors.length" class="error--text">{{
                  errors[0]
                }}</span>
              </v-row> -->
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn class="error" small @click="close_passport_info">
              Cancel
            </v-btn>
            <v-btn class="primary" small @click="save_passport_info"
              >Save</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="bank_info" max-width="500px">
        <v-card>
          <v-card-actions>
            <span class="headline">Bank Info </span>
            <v-spacer></v-spacer>
          </v-card-actions>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("bank name")
                    }}</label>
                    <input v-model="BankInfo.bank_name" class="form-control" />
                    <span
                      v-if="errors && errors.bank_name"
                      class="text-danger mt-2"
                      >{{ errors.bank_name[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("account no")
                    }}</label>
                    <input v-model="BankInfo.account_no" class="form-control" />
                    <span
                      v-if="errors && errors.account_no"
                      class="text-danger mt-2"
                      >{{ errors.account_no[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("account title")
                    }}</label>
                    <input
                      v-model="BankInfo.account_title"
                      class="form-control"
                    />
                    <span
                      v-if="errors && errors.account_title"
                      class="text-danger mt-2"
                      >{{ errors.account_title[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6">
                  <div class="form-group">
                    <label class="col-form-label">{{ caps("iban") }}</label>
                    <input v-model="BankInfo.iban" class="form-control" />
                    <span
                      v-if="errors && errors.iban"
                      class="text-danger mt-2"
                      >{{ errors.iban[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="12">
                  <a
                    href="javascrip:void(0)"
                    @click="add_other_bank_info = !add_other_bank_info"
                    >{{
                      caps(
                        `${add_other_bank_info ? "hide" : "show"} other field`
                      )
                    }}</a
                  >
                </v-col>

                <v-col cols="6" v-if="add_other_bank_info">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("other text")
                    }}</label>
                    <input v-model="BankInfo.other_text" class="form-control" />
                    <span
                      v-if="errors && errors.other_text"
                      class="text-danger"
                      >{{ errors.other_text[0] }}</span
                    >
                  </div>
                </v-col>

                <v-col cols="6" v-if="add_other_bank_info">
                  <div class="form-group">
                    <label class="col-form-label">{{
                      caps("other value")
                    }}</label>
                    <input
                      v-model="BankInfo.other_value"
                      class="form-control"
                    />
                    <span
                      v-if="errors && errors.other_value"
                      class="text-danger mt-2"
                      >{{ errors.other_value[0] }}</span
                    >
                  </div>
                </v-col>

                <span v-if="errors && errors.length" class="error--text">{{
                  errors
                }}</span>
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn class="error" small @click="close_bank_info">
              Cancel
            </v-btn>
            <v-btn class="primary" small @click="save_bank_info">Save</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="documents" max-width="500px">
        <v-card>
          <v-card-actions>
            <span class="headline">{{ caps(`documents`) }}</span>
            <v-spacer></v-spacer>
            <v-btn dark class="primary" fab @click="addDocumentInfo" x-small>
              <v-icon>mdi-plus</v-icon>
            </v-btn>
          </v-card-actions>
          <v-card-text>
            <v-container>
              <v-row v-for="(d, index) in Document.items" :key="index">
                <v-col cols="5">
                  <v-text-field v-model="d.title" label="Title"></v-text-field>

                  <span
                    v-if="errors && errors.title"
                    class="text-danger mt-2"
                    >{{ errors.title[0] }}</span
                  >
                </v-col>
                <v-col cols="5">
                  <div class="form-group">
                    <v-file-input
                      v-model="d.file"
                      placeholder="Upload your file"
                      label="Attachment"
                    >
                      <template v-slot:selection="{ text }">
                        <v-chip v-if="text" small label color="primary">
                          {{ text }}
                        </v-chip>
                      </template>
                    </v-file-input>

                    <span
                      v-if="errors && errors.attachment"
                      class="text-danger mt-2"
                      >{{ errors.attachment[0] }}</span
                    >
                  </div>
                </v-col>
                <v-col cols="2">
                  <div class="form-group">
                    <v-btn
                      dark
                      class="error mt-5"
                      fab
                      @click="removeItem(index)"
                      x-small
                    >
                      <v-icon>mdi-delete</v-icon>
                    </v-btn>
                  </div>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn class="error" small @click="close_document_info">
              Cancel
            </v-btn>
            <v-btn
              :disabled="!Document.items.length"
              class="primary"
              small
              @click="save_document_info"
              >Save</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-card elevation="2">
        <v-row>
          <v-col cols="6" style="border-right:1px dashed #808080;">
            <v-list-item>
              <v-list-item-avatar tile size="120">
                <v-img
                style="border-radius:50%; height:125px; width:50%; margin:0 auto;"
                  :src="payload.profile_picture || '/no-image.png'"
                ></v-img>
              </v-list-item-avatar>
              <v-list-item-content>
                <div class="text-overline mb-1">
                  Employee Id : {{ payload && payload.employee_id || "---" }}
                </div>
                <div class="text-overline mb-1">
                  User Name : {{ payload.user && payload.user.name || "---" }}
                </div>
              </v-list-item-content>
            </v-list-item>

            <v-list-item>
              <v-list-item-content>
                <v-row class="mt-2">
                  <v-col cols="3">
                    <v-list-item-title class="text-h7 mb-1">
                      First Name
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="8">
                    {{ payload.first_name && payload.first_name || "---" }}

                  </v-col>

                  <v-col cols="3">
                    <v-list-item-title class="text-h7 mb-1">
                      Last Name
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="8">
                    {{ payload.last_name && payload.last_name || "---" }}
                  </v-col>

                  <v-col cols="3">
                    <v-list-item-title class="text-h7 mb-1">
                      Department
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="8">
                    {{ payload.department && payload.department.name || "---" }}
                  </v-col>

                  <v-col cols="3">
                    <v-list-item-title class="text-h7 mb-1">
                      Designation
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="8">
                    {{ payload.designation && payload.designation.name || "---" }}
                  </v-col>
                </v-row>
              </v-list-item-content>
            </v-list-item>
          </v-col>
          <v-col cols="6">
            <v-row>
              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  Phone Number
                </v-list-item-title>
              </v-col>
              <v-col cols="3">
                {{ payload.phone_number }}
              </v-col>

              <v-col cols="3" class="text-right" style="margin:-8px;">
                <v-icon
                  v-if="can(`employee_edit`)"
                  @click="editItem(`/employees/${$route.params.id}`)"
                  small
                  class="grey"
                  style="border-radius:50%; padding:5px;"
                  color="secondary"
                  >mdi-pencil</v-icon
                >
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  Phone Number (Relative)
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.phone_relative_number }}
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  Whatsapp Number
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.whatsapp_number }}
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  Whatsapp Number (Relative)
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.whatsapp_relative_number }}
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  User Name
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.user.name }}
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  User Email
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.user.email }}
              </v-col>

              <v-col cols="6">
                <v-list-item-title class="text-h7 mb-1">
                  Joining Date
                </v-list-item-title>
              </v-col>
              <v-col cols="6">
                {{ payload.show_joining_date }}
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-card>
      <v-card>
        <v-tabs class="mt-5 mb-5">
          <v-tab v-if="can(`employee_access`)">
            <v-icon>
              mdi-details
            </v-icon>
            Personal info
          </v-tab>

          <v-tab v-if="can(`employee_access`)">
            <v-icon left>
              mdi-bank
            </v-icon>
            Bank info
          </v-tab>

          <v-tab v-if="can(`employee_access`)">
            <v-icon left>
              mdi-file
            </v-icon>
            Documents
          </v-tab>

          <v-tab-item v-if="can(`employee_access`)">
            <v-row class="pl-1 mt-5 mb-5">
              <v-col cols="6">
                <v-row class="pa-4">
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Passport No.
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="4">
                    {{ editedItem.passport_no || "---" }}
                  </v-col>

                  <v-col cols="3" class="text-right" style="margin:-8px;">
                    <v-icon
                      v-if="can(`employee_edit`)"
                      @click="passport_info = true"
                      small
                      class="grey"
                      style="border-radius:50%; padding:5px;"
                      color="secondary"
                      >mdi-pencil</v-icon
                    >
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Passport Exp Date.
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.show_passport_expiry || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Tel
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.tel || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Nationality
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.nationality || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Religion
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.religion || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Marital status
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.marital_status || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      Employment of spouse
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.no_of_spouse || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      No. of children
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ editedItem.no_of_children || "---" }}
                  </v-col>

                  <v-col cols="5" v-if="editedItem && editedItem.other_text">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ editedItem.other_text || "---" }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7" v-if="editedItem && editedItem.other_value">
                    {{ editedItem.other_value || "---" }}
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </v-tab-item>
          <v-tab-item v-if="can(`employee_access`)">
            <v-row class="pl-1 mt-5 mb-5">
              <v-col cols="6">
                <v-row class="pa-4">
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ caps(`Bank name`) }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="4">
                    {{ BankInfo.bank_name || "---" }}
                  </v-col>

                  <v-col cols="3" class="text-right" style="margin:-8px;">
                    <v-icon
                      v-if="can(`employee_edit`)"
                      @click="bank_info = true"
                      small
                      class="grey"
                      style="border-radius:50%; padding:5px;"
                      color="secondary"
                      >mdi-pencil</v-icon
                    >
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ caps(`Bank account No`) }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ BankInfo.account_no || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ caps(`Bank account Title`) }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ BankInfo.account_title || "---" }}
                  </v-col>
                  <v-col cols="5">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ caps(`iban`) }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7">
                    {{ BankInfo.iban || "---" }}
                  </v-col>
                  <v-col cols="5" v-if="BankInfo && BankInfo.other_text">
                    <v-list-item-title class="text-h7 mb-1">
                      {{ BankInfo.other_text || "---" }}
                    </v-list-item-title>
                  </v-col>
                  <v-col cols="7" v-if="BankInfo && BankInfo.other_value">
                    {{ BankInfo.other_value || "---" }}
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </v-tab-item>

          <v-tab-item v-if="can(`employee_access`)">
            <v-row class="pl-1 mt-5 mb-5">
              <v-col cols="6">
                <v-row
                  v-for="(d, index) in document_list"
                  :key="index"
                  class="pa-2"
                >
                  <v-col cols="5">
                    <span>{{d.title}}</span>
                  </v-col>
                  <v-col cols="4">
                    <a :href="d.attachment" target="_blank">
                      <v-btn x-small class="primary">
                        open file
                      </v-btn>
                    </a>
                    <v-icon color="error" @click="delete_document(d.id)">
                      mdi-delete </v-icon
                    >
                  </v-col>

                  <v-col cols="3" class="text-right" style="margin:-8px;">
                    <v-icon
                      v-if="can(`employee_edit`) && !index"
                      @click="documents = true"
                      small
                      class="grey"
                      style="border-radius:50%; padding:5px;"
                      color="secondary"
                      >mdi-pencil</v-icon
                    >
                  </v-col>
                </v-row>
                <v-col v-if="document_list.length == 0">
                 <v-icon
                      @click="documents = true"
                      small
                      class="grey"
                      style="border-radius:50%; padding:5px;"
                      color="secondary"
                      >mdi-pencil</v-icon
                    >
                  <!-- <v-btn v-if="can(`employee_edit`)" @click="documents = true" x-small class="primary">{{caps(`add document`)}}</v-btn>                   -->
                </v-col>
              </v-col>
            </v-row>
          </v-tab-item>
        </v-tabs>
      </v-card>
    </div>
    <Preloader v-else />
  </div>
  <NoAccess v-else />
</template>

<script>
import Preloader from "../../../components/Preloader.vue";
import NoAccess from "../../../components/NoAccess.vue";

export default {
  data: () => ({
    add_other_personal_info: false,
    add_other_bank_info: false,
    loading: true,
    payload: {},
    passport_info: false,
    bank_info: false,
    documents: false,
    e1: 1,
    errors: [],
    data: [],
    devices: [],
    snackbar: false,
    response: "",

    editedItem: {
      passport_no: "",
      passport_expiry: "",
      tel: "",
      nationality: "",
      religion: "",
      marital_status: "",
      no_of_spouse: "",
      no_of_children: "",
      company_id: "",
      employee_id: ""
    },
    BankInfo: {
      bank_name: "",
      account_no: "",
      account_title: "",
      iban: "",
      company_id: "",
      employee_id: ""
    },
    Document: {
      items: [{ title: "", file: "" }]
    },
    document_list: []
  }),
  async created() {
    this.getEmployeeDetails();
    this.getPassportInfo();
    this.getBankInfo();
    this.getDocumentInfo();

    // let dt = new Date();

    // let y = dt.getFullYear();
    // let m = this.addZeroForLessThanTen(dt.getMonth() + 1);
    // let d = this.addZeroForLessThanTen(dt.getDate());

    // let final_date = y + "-" + m + "-" + d;

    // this.editedItem = {
    //   ...this.editedItem,
    //   passport_no : Math.floor((Math.random() * 10009998760) + 1),
    //   passport_expiry : final_date,
    //   tel : Math.floor((Math.random() * 10009998760) + 1)
    // };
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    passport_info(val) {
      val || this.close_passport_info();
    },
    bank_info(val) {
      val || this.close_bank_info();
    },
    documents(val) {
      val || this.close_document_info();
    },

    options: {
      handler() {
        this.getDataFromApi();
      },
      deep: true
    }
  },
  methods: {
    mapper(obj) {
      let payload = new FormData();

      for (let x in obj) {
        payload.append(x, obj[x]);
      }

      return payload;
    },
    addZeroForLessThanTen(n) {
      return n > 10 ? n : "0" + n;
    },
    caps(str) {
      return str.replace(/\b\w/g, c => c.toUpperCase());
    },
    addDocumentInfo() {
      this.Document.items.push({
        title: "",
        file: ""
      });
    },
    removeItem(index) {
      this.Document.items.splice(index, 1);
    },
    save_passport_info() {
      let payload = {
        ...this.editedItem,
        company_id: this.$auth?.user?.company?.id,
        employee_id: parseInt(this.$route.params.id)
      };

      delete payload.show_passport_expiry;

      this.$axios
        .post(`personalinfo`, payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.errors = [];
            this.snackbar = true;
            this.response = data.message;
            this.close_passport_info();
          }
        })
        .catch(e => console.log(e));
    },
    save_bank_info() {
      let payload = {
        ...this.BankInfo,
        company_id: this.$auth?.user?.company?.id,
        employee_id: parseInt(this.$route.params.id)
      };

      this.$axios
        .post(`bankinfo`, payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.errors = [];
            this.snackbar = true;
            this.response = data.message;
            this.close_bank_info();
          }
        })
        .catch(e => console.log(e));
    },
    save_document_info() {
      let options = {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      };
      let payload = new FormData();

      this.Document.items.forEach(e => {
        payload.append(`items[][title]`, e.title);
        payload.append(`items[][file]`, e.file || {});
      });

      payload.append(`company_id`, this.$auth?.user?.company?.id);
      payload.append(`employee_id`, parseInt(this.$route.params.id));

      this.$axios
        .post(`documentinfo`, payload, options)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.errors = [];
            this.snackbar = true;
            this.response = data.message;
            this.getDocumentInfo();
            this.Document.items = [
              {title : "", file : ""}
            ];
            this.close_document_info();
          }
        })
        .catch(e => console.log(e));
    },
    close_passport_info() {
      this.passport_info = false;
      this.errors = [];
      setTimeout(() => {}, 300);
    },
    close_bank_info() {
      this.bank_info = false;
      this.errors = [];
      setTimeout(() => {}, 300);
    },
    close_document_info() {
      this.documents = false;
      this.errors = [];
      setTimeout(() => {}, 300);
    },
    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },
    getPassportInfo() {
      this.$axios
        .get(`personalinfo/${this.$route.params.id}`)
        .then(({ data }) => {
          this.editedItem = {
            ...data,
            passport_expiry: data.passport_expiry
              ? this.formatted_date(data.passport_expiry)
              : ""
          };
          this.loading = false;
        });
    },

    getBankInfo() {
      this.$axios.get(`bankinfo/${this.$route.params.id}`).then(({ data }) => {
        this.BankInfo = {
          ...data
        };
        this.loading = false;
      });
    },
    getDocumentInfo() {
      this.$axios
        .get(`documentinfo/${this.$route.params.id}`)
        .then(({ data }) => {
          this.document_list = data;
          this.loading = false;
        });
    },

    getEmployeeDetails() {
      this.$axios.get(`employee/${this.$route.params.id}`).then(({ data }) => {
        this.payload = data;
        this.loading = false;
      });
    },
    formatted_date(v) {
      let [year, month, date] = v.split("/");
      return `${year}-${month}-${date}`;
    },
    editItem(item) {
      this.$router.push(item);
    },
    delete_document(id) {
      this.$axios
        .delete(`documentinfo/${id}`)
        .then(({ data }) => {

          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.errors = [];
            this.snackbar = true;
            this.response = data.message;
            this.getDocumentInfo();
            this.close_document_info();
          }
        })
        .catch(e => console.log(e));

    }
  },
  components: { NoAccess, Preloader }
};
</script>
