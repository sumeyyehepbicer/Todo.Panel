<template>
  <b-card>
    <b-card-text>
      <span> Barkod: </span>
    </b-card-text>
    <b-form-group>
      <b-form-input
        ref="barcodeInput"
        id="input-formatter"
        v-model="barcode"
        placeholder="Barkod giriniz"
        @keyup.enter="EntryBarcode(barcode)"
        autofocus
      />
    </b-form-group>
    <br />
    <b-row>
      <b-col md="9">
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
                <span>EKLENEN ÜRÜNLER</span>
              </b-col>
            </b-row>
          </div>
          <b-table
            ref="refUserListTable"
            class="position-relative"
            :items="products"
            responsive
            :fields="tableColumns"
            primary-key="id"
            sort-by.sync="id"
            show-empty
            empty-text="Ürün bulunamadı"
            sort-desc.sync="true"
          >
            <template #cell(actions)="data">
              <div>
                <b-form-spinbutton
                  v-model="data.item.count"
                  size="sm"
                  class="ml-75"
                  min="0"
                  inline
                  @change="BeZero($event, data.item.barcode)"
                />
              </div>
            </template>
          </b-table>
        </b-card>
      </b-col>
      <b-col md="3">
        <div class="checkout-options">
          <b-card>
            <label class="section-label mb-1">HESAP ÖZETİ</label>
            <div class="price-details">
              <ul class="list-unstyled">
                <li class="price-detail">
                  <div>
                    <div class="detail-title detail-total">Toplam Fiyat</div>
                    <div class="detail-amt font-weight-bolder">
                      {{ totalPrice }} TL
                    </div>
                  </div>
                </li>
              </ul>
              <b-button variant="primary" @click="ComplatedFunction()" block>
                İşlemi Tamamla
              </b-button>
            </div>
          </b-card>
        </div>
      </b-col>
    </b-row>
  </b-card>
</template>

<script>
import {
  BButton,
  BCol,
  BCard,
  BInputGroup,
  BFormGroup,
  BFormInput,
  BInputGroupAppend,
  BCardBody,
  BLink,
  BImg,
  BCardText,
  BDropdown,
  BDropdownItem,
  BTable,
  BBadge,
  BFormSpinbutton,
  BRow,
} from "bootstrap-vue";

export default {
  components: {
    BButton,
    BCol,
    BCard,
    BCardText,
    BTable,
    BFormGroup,
    BInputGroup,
    BFormInput,
    BInputGroupAppend,
    BCardBody,
    BLink,
    BDropdown,
    BDropdownItem,
    BImg,
    BBadge,
    BFormSpinbutton,
    BRow,
  },
  data() {
    return {
      request: { Lines: [] },

      barcode: "",
      totalPrice: 0,
      timestamp: "",
      products: [],
      tableColumns: [
        {
          key: "barcode",
          label: "Barkod",
          sortable: true,
        },
        {
          key: "name",
          label: "Adı",
          sortable: true,
        },
        {
          key: "sellPrice",
          label: "Satış Fiyatı",
          sortable: true,
        },
        {
          key: "currencyUnit.name",
          label: "Para Birimi",
          sortable: true,
        },
        {
          key: "unitType.name",
          label: "Birim Tipi",
          sortable: true,
        },
        { key: "actions", label: "İşlemler" },
      ],
    };
  },
  methods: {
    BeZero(value, barcode) {
      if (value === 0) {
        var product = this.products.filter((s) => s.barcode == barcode)[0];
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      }
      this.calculatePrice();
    },
    resetForm() {
      this.products = [];
    },
    calculatePrice() {
      this.totalPrice = 0;
      for (let i = 0; i < this.products.length; i++) {
        var price = this.products[i].count * this.products[i].sellPrice;
        this.totalPrice += price;
      }
    },

    async EntryBarcode(barcode) {
      try {
        barcode = barcode.trim();
        var arananUrun = this.products.filter((s) => s.barcode == barcode);
        if (arananUrun.length > 0) {
          arananUrun[0].count += 1;
        } else {
          var getBarcodeResponse = await this.$http.get(
            "Products/GetByBarcode?barcode=" + this.barcode
          );
          getBarcodeResponse.data.count = 1;
          this.products.push(getBarcodeResponse.data);
        }
        this.barcode = "";
        this.calculatePrice();
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
      //console.log(getBarcodeResponse.data.id);
    },
    async ComplatedFunction() {
      if (this.products.length == 0) {
        this.$swal({
          title: "UYARI",
          text: "Lütfen önce ürün ekleyiniz.",
          icon: "warning",
          confirmButtonText: "Kapat",
          customClass: {
            confirmButton: "btn btn-primary",
          },
          buttonsStyling: false,
        }).then(async (result) => {
          if (result.value) {
            this.$nextTick(() => this.$refs.barcodeInput.focus());
          }
        });
        return;
      }
      try {
        for (let i = 0; i < this.products.length; i++) {
          var line = {};
          line.productId = this.products[i].id;
          line.quantity = this.products[i].count;
          line.unitPrice = this.products[i].sellPrice;
          line.unitTypeId = this.products[i].unitTypeId;
          line.count = this.products[i].count;

          this.request.Lines.push(line);
        }
        console.log(this.request.Lines);
        var addSalesEntryLineResponse = await this.$http.post(
          "SaleEntries",
          this.request
        );
        this.$swal({
          icon: "success",
          title: "Başarılı!",
          text: `Ürün ekleme işlemi başarılı.`,
          confirmButtonText: "Kapat",
          customClass: {
            confirmButton: "btn btn-success",
          },
        }).then(async (result) => {
          if (result.value) {
            this.$nextTick(() => this.$refs.barcodeInput.focus());
          }
        });
        this.resetForm();
        this.totalPrice = 0;
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
        }).then(async (result) => {
          if (result.value) {
            this.$nextTick(() => this.$refs.barcodeInput.focus());
          }
        });
      }
    },
  },
  async created() {
    this.totalPrice = 0;
  },
};
</script>
