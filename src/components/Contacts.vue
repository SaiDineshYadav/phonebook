<template>
  <div class="q-ma-lg">
    <div class="flex flex-center">
      <h6>Contacts List</h6>
    </div>
    <div class="row">
      <div class="col text-left">
        <q-input filled v-model="searchTerm" label="Search" @keyup="onSearch">
          <template v-if="searchTerm" v-slot:append>
            <q-icon
              name="cancel"
              @click="onClearClick"
              class="cursor-pointer"
            />
          </template>
        </q-input>
      </div>
      <div class="col text-right">
        <q-btn
          color="primary"
          label="Create New Contact"
          @click="onCreateButtonClick"
          no-caps
        />
      </div>
    </div>
    <div class="q-mt-md">
      <q-markup-table>
        <thead>
          <tr>
            <th class="text-left">S.No</th>
            <th class="text-left">Name</th>
            <th class="text-left">Phone Number</th>
            <th></th>
            <th></th>
          </tr>
        </thead>
        <tbody v-if="isLoading">
          <tr v-for="i in 6" :key="i">
            <td class="text-left" v-for="i in 5" :key="i">
              <q-skeleton animation="blink" type="text" height="50px" />
            </td>
          </tr>
        </tbody>
        <tbody v-else>
          <tr
            v-for="(contact, index) in searchTerm == ''
              ? contactsList
              : filteredList"
            :key="index"
          >
            <td class="text-left">{{ index + 1 }}</td>
            <td class="text-left">
              {{ contact["firstName"] }} {{ contact["lastName"] }}
            </td>
            <td class="text-left">{{ contact["phone"] }}</td>
            <td>
              <q-icon
                name="edit"
                class="cursor-pointer"
                size="sm"
                @click="onEditClick(contact)"
              />
            </td>
            <td>
              <q-icon
                name="delete"
                class="cursor-pointer"
                size="sm"
                @click="onDeleteClick(index)"
              />
            </td>
          </tr>
          <tr v-if="contactsList.length == 0">
            <div class="q-pa-xl">No Data to display</div>
          </tr>
        </tbody>
      </q-markup-table>
    </div>
    <AddContact
      v-if="openPopup"
      :popupType="popupType"
      :contactSelected="contactSelected"
      @on-contact-popup-close="onContactPopupClose"
    />
  </div>
</template>
<script>
import axios from "axios";
import AddContact from "components/AddContact.vue";

export default {
  name: "Contacts",
  data() {
    return {
      contactsList: [],
      rows: [],
      columns: [],
      isLoading: true,
      openPopup: false,
      popupType: "add",
      contactSelected: {},
      searchTerm: "",
      filteredList: [],
    };
  },
  components: {
    AddContact,
  },
  mounted() {
    this.fetchContactsList();
  },
  methods: {
    // ***************** Fetch the Contact Details ****************************
    async fetchContactsList() {
      this.isLoading = true;
      await axios
        .get(
          "https://my-json-server.typicode.com/voramahavir/contacts-mock-response/contacts"
        )
        .then((res) => {
          console.log(res);
          if (res["status"] == 200) {
            this.contactsList = res["data"];
          }
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => {
          this.isLoading = false;
        });
    },
    // ***************** Triggers on Create Contact Button is Clicked ****************************
    onCreateButtonClick() {
      this.openPopup = true;
      this.popupType = "add";
    },
    // ***************** Triggers on Edit Contact Button is Clicked ****************************
    onEditClick(contact) {
      this.openPopup = true;
      this.popupType = "edit";
      this.contactSelected = contact;
    },
    // ***************** Triggers on Delete Contact Button is Clicked ****************************
    onDeleteClick(index) {
      this.contactsList.splice(index, 1);
    },
    // ************** Save or update Contact details on popup close ********************
    onContactPopupClose(event) {
      this.openPopup = false;
      if (event["isCreatedOrUpdated"]) {
        if (this.popupType == "add") {
          let params = {
            id: this.contactsList.length + 1,
            ...event["contactDetails"],
          };
          this.contactsList.push(params);
        } else if (this.popupType == "edit") {
          this.contactsList.map((item) => {
            if (item.id == event["contactDetails"]["id"]) {
              item["firstName"] = event["contactDetails"]["firstName"];
              item["lastName"] = event["contactDetails"]["lastName"];
              item["phone"] = event["contactDetails"]["phone"];
            }
          });
        }
      }
    },
    // ************ Fetches the Filtered list **********************
    onSearch() {
      let searchValue = this.searchTerm.toLowerCase();
      this.filteredList = this.contactsList.filter(
        (item) =>
          item.firstName.toLowerCase().includes(searchValue) ||
          item.lastName.toLowerCase().includes(searchValue) ||
          item.phone.includes(searchValue)
      );
    },
    // ***************** Clears the search term and filter results **********************
    onClearClick() {
      this.filteredList = [];
      this.searchTerm = "";
    },
  },
};
</script>
<style lang="scss" scoped></style>
