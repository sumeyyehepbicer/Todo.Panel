<template>
  <b-overlay
    variant="white"
    :show="showLoading"
    spinner-variant="primary"
    blur="0"
    opacity=".75"
    rounded="sm"
  >
    <task-list-add-new
      :is-add-new-task-sidebar-active.sync="isAddNewTaskSidebarActive"
      @show-loading="showOrHideLoading"
      @add-new-row="addNewRow"
    />

    <task-list-edit
      :is-edit-task-sidebar-active.sync="isEditTaskSidebarActive"
      :selected-item="selectedItem"
      @show-loading="showOrHideLoading"
    />

    <b-card no-body class="mb-0">
      <div class="m-2">
        <b-row>
          <b-col
            cols="12"
            md="6"
            class="d-flex align-items-center justify-content-start mb-1 mb-md-0"
          >
          </b-col>

          <!-- Search -->
          <b-col cols="12" md="6">
            <div class="d-flex align-items-center justify-content-end">
              <b-button
                v-if="$can('create', 'Tasks')"
                variant="primary"
                @click="isAddNewTaskSidebarActive = true"
              >
                <span class="text-nowrap">Görev Ekle</span>
              </b-button>
            </div>
          </b-col>
        </b-row>
      </div>

      <b-table
        ref="refUserListTable"
        class="position-relative"
        :items="items"
        responsive
        :fields="tableColumns"
        primary-key="id"
        sort-by.sync="id"
        show-empty
        empty-text="Görev bulunamadı"
        sort-desc.sync="true"
      >
        <template #cell(tag)="data">
          <b-badge :variant="data.item.tag.color">
            <feather-icon icon="StarIcon" class="mr-25" />
            <span>{{ data.item.tag.name }}</span>
          </b-badge>
        </template>
        <template #cell(category)="data">
          <b-badge pill :variant="data.item.category.color">
            {{ data.item.category.name }}
          </b-badge>
        </template>

        <template #cell(status)="data">
          <b-badge :variant="data.item.status.color">
            {{ data.item.status.name }}
          </b-badge>
        </template>
        <template #cell(actions)="data">
          <b-dropdown variant="link" no-caret>
            <template #button-content>
              <feather-icon
                icon="MoreVerticalIcon"
                size="16"
                class="align-middle text-body"
              />
            </template>
            <b-dropdown-item
              v-if="$can('update', 'Tasks')"
              @click="
                {
                  selectedItem = data.item;
                  isEditTaskSidebarActive = true;
                }
              "
            >
              <feather-icon icon="EditIcon" />
              <span class="align-middle ml-50">Düzenle</span>
            </b-dropdown-item>

            <b-dropdown-item
              @click="deleteRow(data.item)"
              v-if="$can('delete', 'Tasks')"
            >
              <feather-icon icon="TrashIcon" />
              <span class="align-middle ml-50">Sil</span>
            </b-dropdown-item>
          </b-dropdown>
        </template>
      </b-table>
    </b-card>
  </b-overlay>
</template>

<script>
import {
  BCard,
  BRow,
  BCol,
  BFormInput,
  BButton,
  BTable,
  BMedia,
  BAvatar,
  BLink,
  BBadge,
  BDropdown,
  BDropdownItem,
  BPagination,
  BOverlay,
} from "bootstrap-vue";
import vSelect from "vue-select";
import TaskListAddNew from "./TaskAdd.vue";
import TaskListEdit from "./TaskEdit.vue";
export default {
  components: {
    TaskListAddNew,
    TaskListEdit,
    BCard,
    BRow,
    BOverlay,
    BCol,
    BFormInput,
    BButton,
    BTable,
    BMedia,
    BAvatar,
    BLink,
    BBadge,
    BDropdown,
    BDropdownItem,
    BPagination,
    vSelect,
  },
  data() {
    return {
      showLoading: false,
      isAddNewTaskSidebarActive: false,
      isEditTaskSidebarActive: false,
      items: [],
      selectedItem: null,
      tableColumns: [
        { key: "category", label: "Kategori" },
        {
          key: "title",
          label: "Başlık",
          sortable: true,
        },
        {
          key: "description",
          label: "Açıklama",
          sortable: true,
        },
        {
          key: "point",
          label: "Puan",
          sortable: true,
        },
        {
          key: "user.username",
          label: "Kim Tarafından",
          sortable: true,
        },
        {
          key: "assigned.username",
          label: "Kime",
          sortable: true,
        },
        { key: "tag", label: "Etiket" },

        { key: "status", label: "Durum" },

        { key: "actions", label: "İşlemler" },
      ],
    };
  },
  methods: {
    async fetchData() {
      var dataResponse = await this.$http.get("Tasks/get-all");
      this.items = dataResponse.data;
      console.log(this.items);
    },
    addNewRow(row) {
      this.items.push(row);
    },
    deleteRow(row) {
      this.$swal({
        title: "Silmek istediğinize emin misiniz?",
        text: "Silme işlemi geri alınamaz ve ilişkili tüm kayıtlar silinir!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonText: "Evet, sil!",
        cancelButtonText: "Hayır",
        customClass: {
          confirmButton: "btn btn-primary",
          cancelButton: "btn btn-outline-danger ml-1",
        },
        buttonsStyling: false,
      }).then(async (result) => {
        if (result.value) {
          try {
            this.showLoading = true;
            var deleteResponse = await this.$http.delete(
              "Suppliers?id=" + row.id
            );
            this.items.splice(this.items.indexOf(row), 1);
            this.showLoading = false;
            this.$swal({
              icon: "success",
              title: "Silindi!",
              text: "İşlem başarılı kayıt silindi.",
              confirmButtonText: "Kapat",
              customClass: {
                confirmButton: "btn btn-success",
              },
            });
          } catch (error) {
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
        }
      });
    },
    showOrHideLoading(state) {
      this.showLoading = state;
    },
  },
  async created() {
    this.showLoading = false;
    await this.fetchData();
    this.showLoading = false;
  },
};
</script>

<style></style>
