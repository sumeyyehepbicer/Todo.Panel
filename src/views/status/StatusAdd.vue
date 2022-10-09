<template>
  <b-sidebar
    id="add-new-status-sidebar"
    :visible="isAddNewStatusSidebarActive"
    bg-variant="white"
    sidebar-class="sidebar-lg"
    shadow
    backdrop
    no-header
    right
    @hidden="resetForm"
    @change="(val) => $emit('update:is-add-new-status-sidebar-active', val)"
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
        <h5 class="mb-0">Yeni Durum Ekle</h5>

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
        <b-form-group label="Durum Adı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.name"
            autofocus
            trim
            placeholder="Durum Adı"
          />
        </b-form-group>
        <b-form-group label="Durum Rengi" label-for="full-name">
          <v-select
            v-model="selected"
            label="text"
            :options="options"
            placeholder="Durum Rengi"
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
    prop: "isAddNewStatusSidebarActive",
    event: "update:is-add-new-status-sidebar-active",
  },
  props: {
    isAddNewStatusSidebarActive: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      request: { name: "", color: "" },
      selected: null,
      options: [
        { text: "light-primary" },
        { text: "light-secondary" },
        { text: "light-success" },
        { text: "light-warning" },
        { text: "light-danger" },
        { text: "light-info" },
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
        var addStatusResponse = await this.$http.post(
          "Statuses/create-status",
          this.request
        );

        this.$emit("update:is-add-new-status-sidebar-active", false);
        this.$emit("add-new-row", addStatusResponse.data);
        this.$emit("show-loading", false);
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Durum ekleme işlemi başarılı.`,
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
