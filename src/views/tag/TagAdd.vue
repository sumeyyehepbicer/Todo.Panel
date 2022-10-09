<template>
  <b-sidebar
    id="add-new-tag-sidebar"
    :visible="isAddNewTagSidebarActive"
    bg-variant="white"
    sidebar-class="sidebar-lg"
    shadow
    backdrop
    no-header
    right
    @hidden="resetForm"
    @change="(val) => $emit('update:is-add-new-tag-sidebar-active', val)"
  >
    <template #default="{ hide }">
      <!-- Header -->
      <div
        class="
          d-flex
          justify-content-between
          align-items-center
          content-sidebar-header
          px-2
          py-1
        "
      >
        <h5 class="mb-0">Yeni Etiket Ekle</h5>

        <feather-icon
          class="ml-1 cursor-pointer"
          icon="XIcon"
          size="16"
          @click="hide"
        />
      </div>

      <!-- BODY -->
      <!-- Form -->
      <b-form class="p-2" @submit.prevent="sendForm" @reset.prevent="resetForm">
        <!-- Full Name -->
        <b-form-group label="Etiket Adı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.name"
            autofocus
            trim
            placeholder="Etiket Adı"
          />
        </b-form-group>
        <b-form-group label="Etiket Rengi" label-for="full-name">
          <v-select
            v-model="selected"
            label="text"
            :options="options"
            placeholder="Etiket Rengi"
          />
        </b-form-group>
        <!-- Form Actions -->
        <div class="d-flex mt-2">
          <b-button
            v-ripple.400="'rgba(255, 255, 255, 0.15)'"
            variant="primary"
            class="mr-2"
            type="submit"
          >
            Ekle
          </b-button>
          <b-button
            v-ripple.400="'rgba(186, 191, 199, 0.15)'"
            type="button"
            variant="outline-secondary"
            @click="hide"
          >
            Vazgeç
          </b-button>
        </div>
      </b-form>
    </template>
  </b-sidebar>
</template>

<script>
import {
  BSidebar,
  BForm,
  BFormGroup,
  BFormInput,
  BFormInvalidFeedback,
  BButton,
  BFormSelect,
} from "bootstrap-vue";
import Ripple from "vue-ripple-directive";
import vSelect from "vue-select";

export default {
  components: {
    BSidebar,
    BForm,
    BFormGroup,
    BFormInput,
    BFormInvalidFeedback,
    BButton,
    vSelect,
    BFormSelect,
  },

  directives: {
    Ripple,
  },
  model: {
    prop: "isAddNewTagSidebarActive",
    event: "update:is-add-new-tag-sidebar-active",
  },
  props: {
    isAddNewTagSidebarActive: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      request: { name: "", color: "" },
      selected: null,
      options: [
        { text: "primary" },
        { text: "secondary" },
        { text: "success" },
        { text: "warning" },
        { text: "danger" },
        { text: "info" },
      ],
    };
  },
  methods: {
    resetForm() {
      this.request = {
        name: "",
        color: "",
      };
    },
    async sendForm() {
      if (this.request.name.length == 0) {
        this.$swal({
          title: "UYARI",
          text: "Lütfen tüm alanları doldurunuz.",
          icon: "warning",
          confirmButtonText: "Kapat",
          customClass: {
            confirmButton: "btn btn-primary",
          },
          buttonsStyling: false,
        });
        return;
      }

      try {
        this.request.color = this.selected.text;
        this.$emit("show-loading", true);
        var addTagResponse = await this.$http.post(
          "Tags/create-tag",
          this.request
        );

        this.$emit("update:is-add-new-tag-sidebar-active", false);
        this.$emit("add-new-row", addTagResponse.data);
        this.$emit("show-loading", false);
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Etiket ekleme işlemi başarılı.`,
          confirmButtonText: "Kapat",
          customClass: {
            confirmButton: "btn btn-success",
          },
        });
      } catch (error) {
        this.$emit("show-loading", false);
        this.resetForm();
        this.$swal({
          title: "HATA",
          text: error.response.data.message,
          icon: "error",
          confirmButtonText: "Kapat",
          customClass: {
            confirmButton: "btn btn-primary",
          },
          buttonsStyling: false,
        });
      }
    },
  },
};
</script>

<style lang="scss">
@import "@core/scss/vue/libs/vue-select.scss";

#add-new-user-sidebar {
  .vs__dropdown-menu {
    max-height: 200px !important;
  }
}
</style>
