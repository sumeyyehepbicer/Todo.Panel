<template>
  <b-sidebar
    id="add-new-task-sidebar"
    :visible="isAddNewTaskSidebarActive"
    bg-variant="white"
    sidebar-class="sidebar-lg"
    shadow
    backdrop
    no-header
    right
    @hidden="resetForm"
    @change="(val) => $emit('update:is-add-new-task-sidebar-active', val)"
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
        <h5 class="mb-0">Yeni Görev Ekle</h5>

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
        <b-form-group label="Görev Başlığı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.title"
            autofocus
            trim
            placeholder="Görev Başlığı"
          />
        </b-form-group>
        <b-form-group label="Görev Açıklaması" label-for="full-name">
          <b-form-textarea
            id="full-name"
            v-model="request.description"
            autofocus
            trim
            placeholder="Görev Açıklaması"
          />
        </b-form-group>
        <b-form-group label="Görev Puanı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="request.point"
            autofocus
            trim
            placeholder="Görev Puanı"
          />
        </b-form-group>
        <b-form-group label="Kime">
          <v-select
            v-model="selectedUser"
            label="username"
            :options="users"
            placeholder="Kime"
          />
        </b-form-group>
        <b-form-group label="Kategori">
          <v-select
            v-model="selectedCategory"
            label="name"
            :options="categories"
            placeholder="Kategori"
          />
        </b-form-group>
        <b-form-group label="Etiket">
          <v-select
            v-model="selectedTag"
            label="name"
            :options="tags"
            placeholder="Etiket"
          />
        </b-form-group>
        <b-form-group label="Durum">
          <v-select
            v-model="selectedStatus"
            label="name"
            :options="statuses"
            placeholder="Durum"
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
  BFormTextarea,
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
    BFormTextarea,
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
    prop: "isAddNewTaskSidebarActive",
    event: "update:is-add-new-task-sidebar-active",
  },

  props: {
    isAddNewTaskSidebarActive: {
      type: Boolean,
      required: true,
    },
    selectedItem: {
      type: Object,
      require: false,
    },
  },
  data() {
    return {
      request: {
        title: "",
        description: "",
        point: 0,
        statusId: 0,
        assignedId: 0,
        tagId: 0,
        categoryId: 0,
      },
      selected: null,
      options: [
        { text: "light-primary" },
        { text: "light-secondary" },
        { text: "light-success" },
        { text: "light-warning" },
        { text: "light-danger" },
        { text: "light-info" },
      ],
      users: [],
      selectedUser: null,
      categories: [],
      selectedCategory: null,
      tags: [],
      selectedTag: null,
      statuses: [],
      selectedStatus: null,
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
        this.request.title.length == 0 ||
        this.request.description.length == 0 ||
        this.request.point.length == 0
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
        this.request.categoryId = this.selectedCategory.id;
        this.request.statusId = this.selectedStatus.id;
        this.request.statusId = this.selectedStatus.id;
        this.request.assignedId = this.selectedUser.id;
        this.request.tagId = this.selectedTag.id;
        this.request.categoryId = this.selectedCategory.id;
        this.$emit("show-loading", true);
        var addTaskResponse = await this.$http.post(
          "Tasks/create-task",
          this.request
        );

        this.$emit("update:is-add-new-task-sidebar-active", false);
        this.$emit("add-new-row", addTaskResponse.data);
        this.$emit("show-loading", false);
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Görev ekleme işlemi başarılı.`,
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
    async usersGet() {
      var dataResponse = await this.$http.get("Users/get-all");
      this.users = dataResponse.data;
    },
    async categoriesGet() {
      var dataResponse = await this.$http.get("Categories/get-all");
      this.categories = dataResponse.data;
    },
    async tagsGet() {
      var dataResponse = await this.$http.get("Tags/get-all");
      this.tags = dataResponse.data;
    },
    async statusesGet() {
      var dataResponse = await this.$http.get("Statuses/get-all");
      this.statuses = dataResponse.data;
    },
  },
  async created() {
    await this.usersGet();
    await this.categoriesGet();
    await this.tagsGet();
    await this.statusesGet();
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
