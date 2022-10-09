<template>
  <b-sidebar
    id="add-new-admin-sidebar"
    :visible="isAddNewAdminSidebarActive"
    bg-variant="white"
    sidebar-class="sidebar-lg"
    shadow
    backdrop
    no-header
    right
    @hidden="resetForm"
    @change="(val) => $emit('update:is-add-new-admin-sidebar-active', val)"
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
        <h5 class="mb-0">Yeni Admin Ekle</h5>

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
        <b-form-group label="Adı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.firstName"
            autofocus
            trim
            placeholder="Adı"
          />
        </b-form-group>
        <b-form-group label="Soyadı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.lastName"
            autofocus
            trim
            placeholder="Soyadı"
          />
        </b-form-group>
        <b-form-group label="Kullanıcı Adı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.username"
            autofocus
            trim
            placeholder="Kullanıcı Adı"
          />
        </b-form-group>
        <b-form-group label="Şifre" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.password"
            autofocus
            trim
            placeholder="Şifre"
          />
        </b-form-group>
        <b-form-group label="Eposta" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.email"
            autofocus
            trim
            placeholder="Eposta"
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
    prop: "isAddNewAdminSidebarActive",
    event: "update:is-add-new-user-sidebar-active",
  },
  props: {
    isAddNewAdminSidebarActive: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      request: {
        firstName: "",
        lastName: "",
        username: "",
        password: "",
        email: "",
        RoleId: 0,
        permissions: [],
      },
      permissionList: [],
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
      if (
        this.request.firstName.length == 0 ||
        this.request.lastName.length == 0 ||
        this.request.username.length == 0 ||
        this.request.password.length == 0 ||
        this.request.email.length == 0
      ) {
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
        this.$emit("show-loading", true);
        this.request.RoleId = 3;
        var addUserResponse = await this.$http.post(
          "Users/create-admin",
          this.request
        );

        this.$emit("update:is-add-new-admin-sidebar-active", false);
        this.$emit("add-new-row", addUserResponse.data);
        this.$emit("show-loading", false);
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Kullanıcı ekleme işlemi başarılı.`,
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
    async fetchData() {
      var dataResponse = await this.$http.get("Permissions/get-all");
      this.permissionList = dataResponse.data;
    },
  },
  async created() {
    await this.fetchData();
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
