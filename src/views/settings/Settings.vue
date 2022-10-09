<template>
  <b-card title="Ayarlar">
    <b-tabs>
      <b-tab active>
        <template #title>
          <feather-icon icon="HomeIcon" />
          <span>Kategori</span>
        </template>
        <div>
          <b-overlay
            variant="white"
            :show="showLoading"
            spinner-variant="primary"
            blur="0"
            opacity=".75"
            rounded="sm"
          >
            <category-list-add-new
              :is-add-new-category-sidebar-active.sync="
                isAddNewCategorySidebarActive
              "
              @show-loading="showOrHideLoading"
              @add-new-row="addNewRow"
            />

            <category-list-edit
              :is-edit-category-sidebar-active.sync="
                isEditCategorySidebarActive
              "
              :selected-item="selectedItem"
              @show-loading="showOrHideLoading"
            />

            <b-card no-body class="mb-0">
              <div class="m-2">
                <b-row>
                  <b-col
                    cols="12"
                    md="6"
                    class="
                      d-flex
                      align-items-center
                      justify-content-start
                      mb-1 mb-md-0
                    "
                  >
                  </b-col>

                  <!-- Search -->
                  <b-col cols="12" md="6">
                    <div class="d-flex align-items-center justify-content-end">
                      <b-button
                        v-if="$can('create', 'Categories')"
                        variant="primary"
                        @click="isAddNewCategorySidebarActive = true"
                      >
                        <span class="text-nowrap">Kategori Ekle</span>
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
                empty-text="Kategori bulunamadı"
                sort-desc.sync="true"
              >
                <template #cell(color)="data">
                  <b-badge pill :variant="data.item.color">
                    {{ data.item.color }}
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
                      v-if="$can('update', 'Categories')"
                      @click="
                        {
                          selectedItem = data.item;
                          isEditCategorySidebarActive = true;
                        }
                      "
                    >
                      <feather-icon icon="EditIcon" />
                      <span class="align-middle ml-50">Düzenle</span>
                    </b-dropdown-item>

                    <b-dropdown-item
                      @click="deleteRow(data.item)"
                      v-if="$can('delete', 'Categories')"
                    >
                      <feather-icon icon="TrashIcon" />
                      <span class="align-middle ml-50">Sil</span>
                    </b-dropdown-item>
                  </b-dropdown>
                </template>
              </b-table>
            </b-card>
          </b-overlay>
        </div>
      </b-tab>
      <b-tab>
        <template #title>
          <feather-icon icon="HomeIcon" />
          <span>Etiket</span>
        </template>
        <div>
          <b-overlay
            variant="white"
            :show="showLoading"
            spinner-variant="primary"
            blur="0"
            opacity=".75"
            rounded="sm"
          >
            <tag-list-add-new
              :is-add-new-tag-sidebar-active.sync="isAddNewTagSidebarActive"
              @show-loading="showOrHideLoading"
              @add-new-row="addNewRow"
            />

            <tag-list-edit
              :is-edit-tag-sidebar-active.sync="isEditTagSidebarActive"
              :selected-item="selectedItemTag"
              @show-loading="showOrHideLoading"
            />

            <b-card no-body class="mb-0">
              <div class="m-2">
                <b-row>
                  <b-col
                    cols="12"
                    md="6"
                    class="
                      d-flex
                      align-items-center
                      justify-content-start
                      mb-1 mb-md-0
                    "
                  >
                  </b-col>

                  <!-- Search -->
                  <b-col cols="12" md="6">
                    <div class="d-flex align-items-center justify-content-end">
                      <b-button
                        v-if="$can('create', 'Tags')"
                        variant="primary"
                        @click="isAddNewTagSidebarActive = true"
                      >
                        <span class="text-nowrap">Etiket Ekle</span>
                      </b-button>
                    </div>
                  </b-col>
                </b-row>
              </div>

              <b-table
                ref="refUserListTable"
                class="position-relative"
                :items="itemTags"
                responsive
                :fields="tableColumnsTag"
                primary-key="id"
                sort-by.sync="id"
                show-empty
                empty-text="Etiket bulunamadı"
                sort-desc.sync="true"
              >
                <template #cell(color)="data">
                  <b-badge pill :variant="data.item.color">
                    {{ data.item.color }}
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
                      v-if="$can('update', 'Tags')"
                      @click="
                        {
                          selectedItemTag = data.item;
                          isEditTagSidebarActive = true;
                        }
                      "
                    >
                      <feather-icon icon="EditIcon" />
                      <span class="align-middle ml-50">Düzenle</span>
                    </b-dropdown-item>

                    <b-dropdown-item
                      @click="deleteRow(data.item)"
                      v-if="$can('delete', 'Tags')"
                    >
                      <feather-icon icon="TrashIcon" />
                      <span class="align-middle ml-50">Sil</span>
                    </b-dropdown-item>
                  </b-dropdown>
                </template>
              </b-table>
            </b-card>
          </b-overlay>
        </div>
      </b-tab>
      <b-tab>
        <template #title>
          <feather-icon icon="EyeOffIcon" />
          <span>Durum</span>
        </template>

        <div>
          <b-overlay
            variant="white"
            :show="showLoading"
            spinner-variant="primary"
            blur="0"
            opacity=".75"
            rounded="sm"
          >
            <status-list-add-new
              :is-add-new-status-sidebar-active.sync="
                isAddNewStatusSidebarActive
              "
              @show-loading="showOrHideLoading"
              @add-new-row="addNewRow"
            />

            <status-list-edit
              :is-edit-status-sidebar-active.sync="isEditStatusSidebarActive"
              :selected-item="selectedItemStatus"
              @show-loading="showOrHideLoading"
            />

            <b-card no-body class="mb-0">
              <div class="m-2">
                <b-row>
                  <b-col
                    cols="12"
                    md="6"
                    class="
                      d-flex
                      align-items-center
                      justify-content-start
                      mb-1 mb-md-0
                    "
                  >
                  </b-col>

                  <!-- Search -->
                  <b-col cols="12" md="6">
                    <div class="d-flex align-items-center justify-content-end">
                      <b-button
                        v-if="$can('create', 'Statuses')"
                        variant="primary"
                        @click="isAddNewStatusSidebarActive = true"
                      >
                        <span class="text-nowrap">Durum Ekle</span>
                      </b-button>
                    </div>
                  </b-col>
                </b-row>
              </div>

              <b-table
                ref="refUserListTable"
                class="position-relative"
                :items="itemStatuses"
                responsive
                :fields="tableColumnsStatus"
                primary-key="id"
                sort-by.sync="id"
                show-empty
                empty-text="Durum bulunamadı"
                sort-desc.sync="true"
              >
                <template #cell(color)="data">
                  <b-badge pill :variant="data.item.color">
                    {{ data.item.color }}
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
                      v-if="$can('update', 'Statuses')"
                      @click="
                        {
                          selectedItemStatus = data.item;
                          isEditStatusSidebarActive = true;
                        }
                      "
                    >
                      <feather-icon icon="EditIcon" />
                      <span class="align-middle ml-50">Düzenle</span>
                    </b-dropdown-item>

                    <b-dropdown-item
                      @click="deleteRow(data.item)"
                      v-if="$can('delete', 'Statuses')"
                    >
                      <feather-icon icon="TrashIcon" />
                      <span class="align-middle ml-50">Sil</span>
                    </b-dropdown-item>
                  </b-dropdown>
                </template>
              </b-table>
            </b-card>
          </b-overlay>
        </div>
      </b-tab>
      <b-tab>
        <template #title>
          <feather-icon icon="UserIcon" />
          <span>Kullanıcı</span>
        </template>

        <div>
          <b-overlay
            variant="white"
            :show="showLoading"
            spinner-variant="primary"
            blur="0"
            opacity=".75"
            rounded="sm"
          >
            <user-list-add-new
              :is-add-new-user-sidebar-active.sync="isAddNewUserSidebarActive"
              @show-loading="showOrHideLoading"
              @add-new-row="addNewRow"
            />
            <admin-list-add-new
              :is-add-new-admin-sidebar-active.sync="isAddNewAdminSidebarActive"
              @show-loading="showOrHideLoading"
              @add-new-row="addNewRow"
            />
            <user-list-edit
              :is-edit-user-sidebar-active.sync="isEditUserSidebarActive"
              :selected-item="selectedItemUser"
              @show-loading="showOrHideLoading"
            />

            <b-card no-body class="mb-0">
              <div class="m-2">
                <b-row>
                  <b-col
                    cols="12"
                    md="6"
                    class="
                      d-flex
                      align-items-center
                      justify-content-start
                      mb-1 mb-md-0
                    "
                  >
                  </b-col>

                  <!-- Search -->
                  <b-row>
                    <b-col cols="12" md="6">
                      <div
                        class="d-flex align-items-center justify-content-end"
                      >
                        <b-button
                          v-if="$can('create', 'Users')"
                          variant="primary"
                          @click="isAddNewUserSidebarActive = true"
                        >
                          <span class="text-nowrap">Kullanıcı Ekle</span>
                        </b-button>
                      </div>
                    </b-col>
                    <b-col cols="12" md="6">
                      <div
                        class="d-flex align-items-center justify-content-end"
                      >
                        <b-button
                          v-if="$can('create', 'Users')"
                          variant="primary"
                          @click="isAddNewAdminSidebarActive = true"
                        >
                          <span class="text-nowrap">Admin Ekle</span>
                        </b-button>
                      </div>
                    </b-col>
                  </b-row>
                </b-row>
              </div>

              <b-table
                ref="refUserListTable"
                class="position-relative"
                :items="itemUsers"
                responsive
                :fields="tableColumnsUser"
                primary-key="id"
                sort-by.sync="id"
                show-empty
                empty-text="Kullanıcı bulunamadı"
                sort-desc.sync="true"
              >
                <template #cell(color)="data">
                  <b-badge pill :variant="data.item.color">
                    {{ data.item.color }}
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
                      v-if="$can('update', 'Users')"
                      @click="
                        {
                          selectedItemUser = data.item;
                          isEditUserSidebarActive = true;
                        }
                      "
                    >
                      <feather-icon icon="EditIcon" />
                      <span class="align-middle ml-50">Düzenle</span>
                    </b-dropdown-item>

                    <b-dropdown-item
                      @click="deleteRow(data.item)"
                      v-if="$can('delete', 'Users')"
                    >
                      <feather-icon icon="TrashIcon" />
                      <span class="align-middle ml-50">Sil</span>
                    </b-dropdown-item>
                  </b-dropdown>
                </template>
              </b-table>
            </b-card>
          </b-overlay>
        </div>
      </b-tab>
    </b-tabs>
  </b-card>
</template>

<script>
import { ValidationProvider, ValidationObserver } from "vee-validate";
import BCardCode from "@core/components/b-card-code";
import CategoryListAddNew from "../category/CategoryAdd.vue";
import CategoryListEdit from "../category/CategoryEdit.vue";
import TagListAddNew from "../tag/TagAdd.vue";
import TagListEdit from "../tag/TagEdit.vue";
import StatusListAddNew from "../status/StatusAdd.vue";
import StatusListEdit from "../status/StatusEdit.vue";
import UserListAddNew from "../user/UserAdd.vue";
import UserListEdit from "../user/UserEdit.vue";
import AdminListAddNew from "../user/AdminAdd.vue";

import {
  BTabs,
  BTab,
  BCardText,
  BFormInput,
  BFormGroup,
  BForm,
  BRow,
  BCol,
  BButton,
  BCard,
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

export default {
  components: {
    AdminListAddNew,
    UserListAddNew,
    UserListEdit,
    StatusListAddNew,
    StatusListEdit,
    TagListAddNew,
    TagListEdit,
    CategoryListAddNew,
    CategoryListEdit,
    ValidationProvider,
    ValidationObserver,
    BCardCode,
    BCardText,
    BTabs,
    BTab,
    BFormInput,
    BFormGroup,
    BForm,
    BRow,
    BButton,
    BCard,
    BCol,
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
  },
  data() {
    return {
      showLoading: false,
      isAddNewCategorySidebarActive: false,
      isEditCategorySidebarActive: false,
      isAddNewUserSidebarActive: false,
      isAddNewAdminSidebarActive: false,
      isEditUserSidebarActive: false,
      isAddNewTagSidebarActive: false,
      isEditTagSidebarActive: false,
      isAddNewStatusSidebarActive: false,
      isEditStatusSidebarActive: false,
      items: [],
      itemTags: [],
      itemStatuses: [],
      itemUsers: [],
      selectedItem: null,
      selectedItemTag: null,
      selectedItemStatus: null,
      selectedItemUser: null,
      tableColumns: [
        {
          key: "name",
          label: "Adı",
          sortable: true,
        },
        {
          key: "color",
          label: "Renk",
          sortable: true,
        },
        { key: "actions", label: "İşlemler" },
      ],
      tableColumnsTag: [
        {
          key: "name",
          label: "Adı",
          sortable: true,
        },
        {
          key: "color",
          label: "Renk",
          sortable: true,
        },
        { key: "actions", label: "İşlemler" },
      ],
      tableColumnsStatus: [
        {
          key: "name",
          label: "Adı",
          sortable: true,
        },
        {
          key: "color",
          label: "Renk",
          sortable: true,
        },
        { key: "actions", label: "İşlemler" },
      ],
      tableColumnsUser: [
        {
          key: "firstName",
          label: "Adı",
          sortable: true,
        },
        {
          key: "lastName",
          label: "Soyadı",
          sortable: true,
        },
        {
          key: "role.description",
          label: "Rol",
          sortable: true,
        },
        { key: "actions", label: "İşlemler" },
      ],
    };
  },
  methods: {
    async fetchData() {
      var dataResponse = await this.$http.get("Categories/get-all");
      this.items = dataResponse.data;
      console.log(this.items);
    },
    async fetchDataTags() {
      var dataResponse = await this.$http.get("Tags/get-all");
      this.itemTags = dataResponse.data;
    },
    async fetchDataStatuses() {
      var dataResponse = await this.$http.get("Statuses/get-all");
      this.itemStatuses = dataResponse.data;
    },
    async fetchDataUsers() {
      var dataResponse = await this.$http.get("Users/get-all");
      this.itemUsers = dataResponse.data;
    },
    addNewRow(row) {
      this.items.push(row);
    },
    showOrHideLoading(state) {
      this.showLoading = state;
    },
  },
  async created() {
    this.showLoading = false;
    await this.fetchData();
    await this.fetchDataTags();
    await this.fetchDataStatuses();
    await this.fetchDataUsers();
    this.showLoading = false;
  },
};
</script>
