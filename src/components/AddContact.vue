<template>
  <q-dialog v-model="showPopup">
    <q-card style="width: 50%">
      <q-card-section class="row items-center q-pb-none">
        <div class="text-h6 text-capitalize">{{ popupType }} Contact</div>
        <q-space />
        <q-btn
          icon="close"
          flat
          round
          dense
          @click="onPopupClose"
          v-close-popup
        />
      </q-card-section>

      <q-card-section>
        <q-form ref="contactForm" @submit="onSubmitClick()">
          <q-input
            ref="fname"
            outlined
            v-model.trim="contactData.firstName"
            label="Contact First Name"
            placeholder="Enter First Name"
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || 'First Name is required',
            ]"
          />
          <q-input
            ref="lname"
            class="q-mt-md"
            outlined
            v-model.trim="contactData.lastName"
            label="Contact Last Name"
            placeholder="Enter Last Name"
          />
          <q-input
            ref="phone"
            class="q-mt-md"
            outlined
            v-model.trim="contactData.phone"
            label="Contact Number"
            placeholder="Enter Phone Number"
            mask="##########"
            unmasked-value
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || 'Phone Number is required',
            ]"
          />
          <div class="flex flex-center q-mt-md">
            <q-btn
              type="submit"
              :label="popupType == 'add' ? 'Save' : 'Update'"
              color="primary"
              no-caps
            />
          </div>
        </q-form>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
<script>
export default {
  data() {
    return {
      showPopup: true,
      isCreatedOrUpdated: false,
      contactData: {
        firstName: "",
        lastName: "",
        phone: "",
        id: "",
      },
    };
  },
  props: ["popupType", "contactSelected"],
  mounted() {
    if (this.popupType == "edit") {
      this.contactData = { ...this.contactSelected };
    }
  },
  methods: {
    onSubmitClick() {
      this.$refs.fname.validate();
      this.$refs.lname.validate();
      this.$refs.phone.validate();
      this.$refs.contactForm.validate().then((success) => {
        console.log(success);
        if (success) {
          this.isCreatedOrUpdated = true;
          this.onPopupClose();
        }
      });
    },
    onPopupClose() {
      this.$emit("on-contact-popup-close", {
        isCreatedOrUpdated: this.isCreatedOrUpdated,
        contactDetails: this.contactData,
      });
      this.showPopup = false;
    },
  },
};
</script>
