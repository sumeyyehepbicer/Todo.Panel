<template>
  <b-sidebar
    id="add-new-task-sidebar"
    :visible="isEditTaskSidebarActive"
    bg-variant="white"
    sidebar-class="sidebar-lg"
    shadow
    backdrop
    no-header
    right
    @hidden="resetForm"
    @change="(val) => $emit('update:is-edit-task-sidebar-active', val)"
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
        <h5 class="mb-0">Görev Düzenle</h5>

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
            v-model="selectedItem.title"
            autofocus
            trim
            placeholder="Görev Başlığı"
          />
        </b-form-group>
        <b-form-group label="Görev Açıklaması" label-for="full-name">
          <b-form-textarea
            id="full-name"
            v-model="selectedItem.description"
            autofocus
            trim
            placeholder="Görev Açıklaması"
          />
        </b-form-group>
        <b-form-group label="Görev Puanı" label-for="full-name">
          <b-form-input
            id="full-name"
            v-model="selectedItem.point"
            autofocus
            trim
            placeholder="Görev Puanı"
          />
        </b-form-group>
        <b-form-group label="Kime">
          <v-select
            v-model="selectedItem.assigned"
            label="username"
            :options="users"
            placeholder="Kime"
          />
        </b-form-group>
        <b-form-group label="Kategori">
          <v-select
            v-model="selectedItem.category"
            label="name"
            :options="categories"
            placeholder="Kategori"
          />
        </b-form-group>
        <b-form-group label="Etiket">
          <v-select
            v-model="selectedItem.tag"
            label="name"
            :options="tags"
            placeholder="Etiket"
          />
        </b-form-group>
        <b-form-group label="Durum">
          <v-select
            v-model="selectedItem.status"
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
            Kaydet
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
  },
  directives: {
    Ripple,
  },
  model: {
    prop: "isEditTaskSidebarActive",
    event: "update:is-edit-task-sidebar-active",
  },
  props: {
    isEditTaskSidebarActive: {
      type: Boolean,
      required: true,
    },
    selectedItem: {
      type: Object,
      require: true,
    },
  },
  data() {
    return {
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
    resetForm() {},
    async sendForm() {
      if (
        this.selectedItem.title.length == 0 ||
        this.selectedItem.description.length == 0 ||
        this.selectedItem.point.length == 0
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

        this.selectedItem.categoryId = this.selectedItem.category.id;
        this.selectedItem.statusId = this.selectedItem.status.id;
        this.selectedItem.assignedId = this.selectedItem.assigned.id;
        this.selectedItem.tagId = this.selectedItem.tag.id;

        var editTaskResponse = await this.$http.post(
          "Tasks/update-task",
          this.selectedItem
        );

        this.$emit("update:is-edit-task-sidebar-active", false);
        this.$emit("show-loading", false);
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Görev güncelleme işlemi başarılı.`,
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
