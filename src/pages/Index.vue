<template>
  <q-page>
    <Contacts />
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import Contacts from "components/Contacts.vue";

export default defineComponent({
  name: "PageIndex",
  components: {
    Contacts,
  },
});
</script>
