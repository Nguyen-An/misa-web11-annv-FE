<template>
  <div class="black-ground bg-add">
    <div class="form-add">
      <div class="form-add__container">
        <div class="form-add__container__head">
          <div class="form-add__container__head__title">{{ TheTitle }}</div>
          <div
            @click="hidenForm"
            class="icon-close"
            :title="Tooltip.Close"
          ></div>
        </div>
        <div class="form-add__container__body">
          <form class="form" id="form-1">
            <div class="form-row">
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.FixedAssetCode }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  @blur="blurValidate($event)"
                  @input="inputValide($event)"
                  id="AssetCode"
                  class="form-control"
                  type="text"
                  name="fname"
                  v-model="FixedAsset.fixedAssetCode"
                  :placeholder="Placeholder.ImportAssetCode"
                  maxlength="36"
                />
                <div class="form-message"></div>
              </div>
              <div class="form-group col2-3">
                <label for="fname"
                  >{{ ContentText.FixedAssetName }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  @blur="blurValidate($event)"
                  @input="inputValide($event)"
                  id="AssetName"
                  class="form-control"
                  type="text"
                  name="fname"
                  v-model="FixedAsset.fixedAssetName"
                  :placeholder="Placeholder.ImportAssetName"
                  maxlength="255"
                />
                <div class="form-message"></div>
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.DepartmentCode }}
                  <span class="cl-red">{{ ContentText.red }}</span></label
                >
                <div class="col-select">
                  <input
                    @click="showOption($event)"
                    id="PartCode"
                    class="option-filter__content"
                    :placeholder="Placeholder.ImportDepartmentCode"
                    :value="getPartCodeByDepartmentId(FixedAsset.departmentId)"
                    readonly
                  />
                  <div
                    class="icon-down select-option"
                    @click="showOption($event)"
                  ></div>
                  <div class="option-filter-items">
                    <div
                      class="option-filter-item"
                      ref="optionFilteritem"
                      v-for="(department, index) in Departments"
                      :key="index"
                      @click="
                        changeOptionPart(
                          $event,
                          department.departmentName,
                          department.departmentId
                        )
                      "
                    >
                      {{ department.departmentName }}
                    </div>
                  </div>
                </div>
              </div>
              <div class="form-group col2-3">
                <label for="fname">{{ ContentText.DepartmentName }} </label>
                <input
                  id="PartName"
                  class="form-control bg-ccc"
                  type="text"
                  name="fname"
                  :value="getPartNameByDepartmentId(FixedAsset.departmentId)"
                  placeholder=""
                  readonly
                />
                <div class="form-message"></div>
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.FixedAssetCategoryCode }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <div class="col-select">
                  <input
                    @click="showOption($event)"
                    class="option-filter__content"
                    :placeholder="Placeholder.ImportCategoryAssetCode"
                    :value="
                      getTypeAssetCodeByFixedAssetCategoryId(
                        FixedAsset.fixedAssetCategoryId
                      )
                    "
                    readonly
                  />
                  <div
                    class="icon-down select-option"
                    @click="showOption($event)"
                  ></div>
                  <div class="option-filter-items">
                    <div
                      class="option-filter-item"
                      ref="optionFilteritem"
                      v-for="(assetCategory, index) in AssetCategorys"
                      :key="index"
                      @click="
                        changeOptionTypeAsset(
                          $event,
                          assetCategory.fixedAssetCategoryName,
                          assetCategory.fixedAssetCategoryId
                        )
                      "
                    >
                      {{ assetCategory.fixedAssetCategoryName }}
                    </div>
                  </div>
                </div>
              </div>
              <div class="form-group col2-3">
                <label for="fname">{{
                  ContentText.FixedAssetCategoryName
                }}</label>
                <input
                  id="TypeAssetName"
                  class="form-control bg-ccc"
                  type="text"
                  name="fname"
                  :value="
                    getTypeAssetNameByFixedAssetCategoryId(
                      FixedAsset.fixedAssetCategoryId
                    )
                  "
                  readonly
                  placeholder=""
                />
                <div class="form-message"></div>
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.Quantity }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <div class="out-ip">
                  <input
                    class="form-control text-align-right padding-right-20"
                    type="text"
                    name="fname"
                    v-model="FixedAsset.quantity"
                    maxlength="5"
                  />
                  <div class="icon-UpAndDown">
                    <div class="icon-up"></div>
                    <div class="icon-down"></div>
                  </div>
                </div>
                <div class="form-message"></div>
              </div>
              <div class="form-group col1-3 form-group col1-3-center">
                <label for="fname"
                  >{{ ContentText.Cost }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  class="form-control text-align-right padding-right-20"
                  ref="cost"
                  type="text"
                  name="fname"
                  @keyup="importCost"
                  value="0"
                  maxlength="25"
                />
              </div>
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.LifeTime }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  class="form-control text-align-right padding-right-20"
                  type="text"
                  name="fname"
                  v-model="FixedAsset.lifeTime"
                  maxlength="3"
                />
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col1-3">
                <label for="fname"
                  >{{ ContentText.DepreciationRate
                  }}<span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  class="form-control text-align-right padding-right-20"
                  type="text"
                  name="fname"
                  v-model="FixedAsset.depreciationRate"
                  maxlength="6"
                />
                <div class="form-message"></div>
              </div>
              <div class="form-group col1-3 form-group col1-3-center">
                <label for="fname"
                  >{{ ContentText.ValueAtrophyYear }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <input
                  class="form-control text-align-right padding-right-20"
                  type="text"
                  ref="valueAtrophyYear"
                  name="fname"
                  @keyup="importValueAtrophyYear"
                  :value="0"
                  maxlength="25"
                />
                <!-- @input="handleInputValueAtrophyYear" -->
              </div>
              <div class="form-group col1-3">
                <label for="fname">{{ ContentText.TrackedYear }} </label>
                <input
                  class="form-control bg-ccc text-align-right padding-right-20"
                  type="text"
                  name="fname"
                  readonly
                  v-model="FixedAsset.trackedYear"
                />
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col1-3 position-relative">
                <label for="fname"
                  >{{ ContentText.BuyDate }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <Datepicker
                  v-model="FixedAsset.purchaseDate"
                  placeholder="dd/mm/yyyy"
                  class="date-picker"
                  hideInputIcon
                  selectText="Ch???n"
                  cancelText="????ng"
                  :clearable="false"
                  :enableTimePicker="false"
                ></Datepicker>
                <div class="icon-datepicker"></div>
              </div>
              <div
                class="
                  form-group
                  col1-3
                  form-group
                  col1-3-center
                  position-relative
                "
              >
                <label for="fname"
                  >{{ ContentText.UseStartDate }}
                  <span class="cl-red">{{ ContentText.red }}</span>
                </label>
                <Datepicker
                  v-model="FixedAsset.useDate"
                  class="date-picker"
                  hideInputIcon
                  selectText="Ch???n"
                  cancelText="????ng"
                  :clearable="false"
                  :enableTimePicker="false"
                ></Datepicker>
                <div style="right: -268px" class="icon-datepicker"></div>
              </div>
              <div class="form-group col1-3"></div>
            </div>
          </form>
        </div>
        <div class="form-add__container__foot">
          <div @click="addData" type="sub" class="btn-submit">
            {{ ContentText.Save }}
          </div>
          <div @click="canceForm" type="submit" class="btn-cancel">
            {{ ContentText.Cancer }}
          </div>
        </div>
      </div>
    </div>
    <dialog-validate
      v-if="isShowDialogValidate"
      :validateItems="validateItems"
      :isShowDialogValidate="isShowDialogValidate"
    />
    <dialogCance v-if="isShowDialogCance" />
  </div>
</template>

<script>
import axios from "axios";
import Enum from "@/js/enum.js";
import Resource from "@/js/resource.js";
// import EnumDTO from "@/js/enumDTO.js";
import { FixedAsset } from "@/class/FixedAsset.js";
import Datepicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";
import dialogValidate from "@/components/dialog/dialogValidate.vue";
import dialogCance from "@/components/dialog/dialogCance.vue";

export default {
  name: "formMode",
  components: { Datepicker, dialogValidate, dialogCance },
  props: {
    // Ch??? ????? c???a popup
    mode: Number,
    updateAsset: Object,
  },
  data() {
    return {
      ContentText: Resource.ContentText, //Table content g???i t??? file resource.js
      Title: Resource.Title, //Ti??u ????? g???i t??? file resource.js
      Placeholder: Resource.Placeholder, //Placeholder g???i t??? file resource.js
      Tooltip: Resource.Tooltip, //Tooltip g???i t??? file resource.js
      FixedAsset: new FixedAsset(), // ?????i t?????ng t??i s???n s??? nh???n data t??? form
      TheTitle: "", // Ti??u ????? c???a form
      AssetCategorys: [], // Danh s??ch lo???i t??a s???n l???y t??? API
      Departments: [], // Danh s??ch b??? ph???n s??? d???ng l???y t??? API
      isShowDialogValidate: false,
      isShowDialogCance: false,
      validateItemss: [], // M???ng c??c tr?????ng ch??a nh???p c???n validate(c???nh b??o)
      assetCodeAuto: "", // M?? t??i s???n t??? ?????ng l???y ???????c
      valueAtrophyYearBefor: "0", //Gi?? tr??? c???a gi?? tr??? hao m??n n??m tr?????c l??c nh???p (tr?????c khi b???t keyup)
      costBefor: "0", // Gi?? tr??? c???a nguy??n gi?? tr?????c l??c nh???p (tr?????c khi b???t keyup)
    };
  },
  created() {
    var _this = this;

    // Load d??? li???u trang
    this.loadData();

    // S??? ki???n khi click ra ngo??i th?? ????ng th??? select
    window.addEventListener("click", function () {
      try {
        // Chon t???t c??? th??? option
        let listSelectControl = _this.$refs.optionFilteritem;

        // ???n h???t to??n b??? th??? select ??ang m???
        for (const selectControl of Array.from(listSelectControl)) {
          selectControl.style.display = "none";
        }
      } catch (error) {
        console.log(error);
      }
    });

    try {
      // c???p nh???t ?????i t?????ng t?????ng ???ng v???i t???ng lo???i popup
      switch (this.mode) {
        case Enum.Mode.Update:
          // Thi???t l???p title popup th??nh S???a t??i s???n
          this.TheTitle = this.ContentText.UpdateAsset;

          // Truy???n gi?? tr??? t??i s???n ban ?????u v??o form
          this.CreateFormUpdate(this.updateAsset);

          break;
        default:
          // Thi???t l???p title popup th??nh Th??m t??i s???n
          this.TheTitle = this.ContentText.AddAsset;

          // T??? sinh m?? m???i cho tr?????ng m?? t??i s???n
          fetch("http://localhost:63515/api/Assets/fixedAssetNewCode")
            .then((response) => {
              return response.json();
            })
            .then((AssetCodeAuto) => {
              this.assetCodeAuto = AssetCodeAuto.newAssetCode;
              this.FixedAsset.fixedAssetCode = this.assetCodeAuto;
            });

          break;
      }
    } catch (error) {
      console.log(error);
    }
  },
  methods: {
    // H??m c???u input Linh
    // handleInputValueAtrophyYear(e) {
    //   e.target.value = e.target.value.toString()
    //     .replace(/\D/g, "")
    //     .replace(/\B(?=(\d{3})+(?!\d))/g, ".")
    // },

    /**
     * H??m Load data
     * T???o b???i: NVAn(20/12/2022)
     */
    loadData() {
      // load danh s??ch t??i s???n
      this.isShowDialogLoading = true;

      // load danh s??ch lo???i t??i s???n
      try {
        fetch("http://localhost:63515/api/AssetCategorys")
          .then((response) => {
            return response.json();
          })
          .then((AssetCategory) => {
            this.AssetCategorys = AssetCategory;
          });
      } catch (error) {
        console.log(error);
      }

      // load danh s??ch ph??ng ban
      try {
        fetch("http://localhost:63515/api/Departments")
          .then((response) => {
            return response.json();
          })
          .then((part) => {
            this.Departments = part;
          });
      } catch (error) {
        console.log(error);
      }
    },

    /**
     * H??m format ti???n str: String
     * Tr??? v??? string
     * T???o b???i: NVAn(20/12/2022)
     */
    formatCash(str) {
      return str
        .split("")
        .reverse()
        .reduce((prev, next, index) => {
          return (index % 3 ? next : next + ".") + prev;
        });
    },

    /**
     * H??m format ti???n str: (String) (X??a d???u ph???y)
     * Tr??? v??? number
     * T???o b???i: NVAn(20/12/2022)
     */
    formatCashReverse(str) {
      return Number(str.split(".").join(""));
    },

    //X??? l?? s??? ki???n khi blur v??o ?? input (???n hi???n message validate)
    blurValidate(e) {
      let errorElement = e.path[1].querySelector(".form-message");
      let inputElement = e.path[1].querySelector("input");
      let errorMessage = e.path[1].querySelector("input").value.trim()
        ? undefined
        : "Vui l??ng nh???p tr?????ng n??y";
      if (errorMessage) {
        errorElement.innerText = errorMessage;
        inputElement.parentElement.classList.add("invalid");
      } else {
        errorElement.innerText = "";
        inputElement.parentElement.classList.remove("invalid");
      }
    },

    //X??? l?? s??? ki???n khi nh???p d??? li???u v??o ?? input (???n message validate)
    inputValide(e) {
      let errorElement = e.path[1].querySelector(".form-message");
      let inputElement = e.path[1].querySelector("input");
      errorElement.innerText = "";
      inputElement.parentElement.classList.remove("invalid");
    },

    //S??? ki???n t???o th??? select(blur v?? ra s??? hi???n option)
    showOption(e) {
      try {
        e.stopImmediatePropagation();
        let selectControls = e.path[1].querySelectorAll(".option-filter-item");
        for (const selectControl of selectControls) {
          selectControl.style.display = "flex";
        }
      } catch (error) {
        console.log(error);
      }
    },

    /**
     * H??m x??? l?? s??? ki???n khi thay ?????i option th??? select b??? ph???n s??? d???ng
     * T???o b???i: NVAn(20/12/2022)
     */
    changeOptionPart(e, departmentName, departmentId) {
      // Thay ?????i value ?? input select
      e.path[2].querySelector(".option-filter__content").value = departmentName;

      // Ch???n t??n b??? ph???n t????ng ???ng v???i m?? b??? ph???n
      for (const department of this.Departments) {
        if (department.departmentId == departmentId) {
          this.FixedAsset.departmentId = departmentId;
        }
      }
    },

    /**
     * H??m t??? ?????ng t??nh gi?? tr??? hao m??n n??m
     * T???o b???i: NVAn(20/12/2022)
     */
    autoCalculateValueAtrophyYear() {
      // L???y th??? input c???a gi?? tr??? hao m??n n??m
      let valueAtrophyYear = this.$refs.valueAtrophyYear;

      // T??? ?????ng t??nh gi?? tr??? hao m??n n??m
      this.FixedAsset.valueAtrophyYear = Math.round(
        (this.FixedAsset.depreciationRate * this.FixedAsset.cost) / 100
      );

      //format ti???n cho gi?? tr??? hao m??n n??m v?? c???p nh???t 
      valueAtrophyYear.value = this.formatCash(this.FixedAsset.valueAtrophyYear.toString());

      // C???p nh???t gi?? tr??? hao m??n n??m c??
      this.valueAtrophyYearBefor = valueAtrophyYear.value;
    },

    /**
     * H??m b???t s??? ki???n keyup cho nguy??n gi??
     * T???o b???i: NVAn(20/12/2022)
     */
    importCost(e) {
      let cost = this.$refs.cost;

      //Ch??? cho nh???p s???
      if ((e.keyCode >= 48 && e.keyCode <= 57) || e.keyCode === 8) {
        //format ti???n cho nguy??n gi??
        cost.value = this.formatCash(
          this.formatCashReverse(cost.value).toString()
        );

        //g??n gi?? tr??? v??o cost
        this.FixedAsset.cost = this.formatCashReverse(cost.value);

        //T??? ?????ng t??nh gi?? tr??? hao m??n n??m
        this.autoCalculateValueAtrophyYear();

        // C???p nh???t nguy??n gi?? c??
        this.costBefor = cost.value;
      } else {
        //N???u nh???p kh??ng ph???i s???

        // g??n value cho gi?? tr??? value c??
        cost.value = this.formatCash(
          this.formatCashReverse(this.costBefor).toString()
        );
      }

      // console.log("costBefor", this.costBefor);
      // console.log("FixedAsset.cost", this.FixedAsset.cost);
      // console.log("cost.value", cost.value);
    },

    /**
     * H??m b???t s??? ki??n keyup cho gi?? tr??? hao m??n n??m
     * T???o b???i: NVAn(20/12/2022)
     */
    importValueAtrophyYear(e) {
      // L???y th??? input c???a gi?? tr??? hao m??n n??m
      let valueAtrophyYear = this.$refs.valueAtrophyYear;

      //Ch??? cho nh???p s???
      if ((e.keyCode >= 48 && e.keyCode <= 57) || e.keyCode === 8) {
        // N???u nh???p s??? s??? th???c hi???n:

        //format ti???n cho gi?? tr??? hao m??n n??m
        valueAtrophyYear.value = this.formatCash(
          this.formatCashReverse(valueAtrophyYear.value).toString()
        );

        //g??n gi?? tr??? v??o gi?? tr??? hao m??n n??m
        this.FixedAsset.valueAtrophyYear = this.formatCashReverse(
          valueAtrophyYear.value
        );

        // C???p nh???t gi?? tr??? hao m??n n??m c??
        this.valueAtrophyYearBefor = valueAtrophyYear.value;
      } else {
        //N???u nh???p kh??ng ph???i s???

        // g??n value cho gi?? tr??? value c??
        valueAtrophyYear.value = this.formatCash(
          this.formatCashReverse(this.valueAtrophyYearBefor).toString()
        );
      }

      // console.log(this.valueAtrophyYearBefor);
      // console.log(valueAtrophyYear.value);
      // console.log(this.FixedAsset.valueAtrophyYear);
    },

    //X??? l?? s??? ki???n khi thay ?????i option th??? select lo???i t??i s???n
    changeOptionTypeAsset(e, fixedAssetCategoryName, fixedAssetCategoryId) {
      // Thay ?????i value ?? input select
      e.path[2].querySelector(".option-filter__content").value =
        fixedAssetCategoryName;

      // Ch???n t??n lo???i t??i s???n t????ng ???ng v???i m?? lo???i t??i s???n
      for (const assetCategory of this.AssetCategorys) {
        if (assetCategory.fixedAssetCategoryId == fixedAssetCategoryId) {
          this.FixedAsset.fixedAssetCategoryId = fixedAssetCategoryId;
        }
      }

      //Ch???n s??? n??m s??? d???ng v?? t??? l??? hao m??n theo lo???i t??i s???n ????
      for (const assetCategory of this.AssetCategorys) {
        if (assetCategory.fixedAssetCategoryId == fixedAssetCategoryId) {
          this.FixedAsset.lifeTime = assetCategory.lifeTime;
          this.FixedAsset.depreciationRate = assetCategory.depreciationRate;
          this.FixedAsset.trackedYear =
            this.FixedAsset.useDate.getYear() + 1900;
        }
      }
    },

    //????ng form khi click close
    hidenForm() {
      try {
        this.$emit("hideForm");
      } catch (error) {
        console.log(error);
      }
    },
    //Click v??o h???y
    canceForm() {
      try {
        this.$emit("hideForm");
      } catch (error) {
        console.log(error);
      }
    },

    // Th??m m???i d??? li???u khi l??u
    addData() {
      try {
        // Khai b??o c??c tr?????ng c???n validate
        let checkValidate = false;
        checkValidate =
          this.FixedAsset.fixedAssetCode == false ||
          this.FixedAsset.fixedAssetName == false ||
          this.FixedAsset.departmentId == false ||
          this.FixedAsset.fixedAssetCategoryId == false ||
          this.FixedAsset.quantity == false ||
          this.FixedAsset.cost == false ||
          this.FixedAsset.lifeTime == false ||
          this.FixedAsset.depreciationRate == false ||
          this.FixedAsset.valueAtrophyYear == false ||
          this.FixedAsset.trackedYear == false;

        // Ki???m tra validate ??? c??c tr?????ng
        if (checkValidate) {
          this.isShowDialogValidate = !this.isShowDialogValidate;
          this.validateItems = [];

          if (this.FixedAsset.fixedAssetCode === "")
            this.validateItems.push("M?? t??i s???n");
          if (this.FixedAsset.fixedAssetName === "")
            this.validateItems.push("T??n t??i s???n");
          if (this.FixedAsset.departmentId === "")
            this.validateItems.push("M?? b??? ph???n s??? d???ng");
          if (this.FixedAsset.fixedAssetCategoryId === "")
            this.validateItems.push("M?? lo???i t??i s???n");
          if (this.FixedAsset.quantity === 0)
            this.validateItems.push("S??? l?????ng");
          if (this.FixedAsset.cost === 0) this.validateItems.push("Nguy??n gi??");
          if (this.FixedAsset.valueAtrophyYear === 0)
            this.validateItems.push("Gi?? tr??? hao m??n n??m");
          if (this.FixedAsset.lifeTime === 0)
            this.validateItems.push("S??? n??m s??? d???ng");
          if (this.FixedAsset.depreciationRate === 0)
            this.validateItems.push("T??? l??? hao m??n");
        } else {
          //Ki???m tra xem thu???c lo???i form mode n??o
          switch (this.mode) {
            case Enum.Mode.Update:
              axios
                .put(
                  `http://localhost:63515/api/Assets/${this.FixedAsset.fixedAssetId}`,
                  {
                    fixedAssetId: this.FixedAsset.fixedAssetId,
                    fixedAssetCode: this.FixedAsset.fixedAssetCode,
                    fixedAssetName: this.FixedAsset.fixedAssetName,
                    departmentId: this.FixedAsset.departmentId,
                    fixedAssetCategoryId: this.FixedAsset.fixedAssetCategoryId,
                    purchaseDate: this.FixedAsset.purchaseDate,
                    cost: this.FixedAsset.cost,
                    quantity: this.FixedAsset.quantity,
                    depreciationRate: this.FixedAsset.depreciationRate,
                    trackedYear: this.FixedAsset.trackedYear,
                    lifeTime: this.lifeTime,
                    productionYear: this.productionYear,
                    valueAtrophyYear: this.valueAtrophyYear,
                    createdBy: "string",
                    createdDate: "2022-12-26T17:50:33.580Z",
                    modifiedBy: "string",
                    modifiedDate: "2022-12-26T17:50:33.580Z",
                  }
                )
                .then((res) => {
                  console.log(res);
                });
              this.$emit("onShowToastSuccess");
              this.canceForm();
              break;

            // M???c ?????nh s??? l?? th??m m???i
            default:
              axios
                .post("http://localhost:63515/api/Assets", {
                  fixedAssetId: "3fa85f64-5717-4562-b3fc-2c963f66afa6",
                  fixedAssetCode: this.FixedAsset.fixedAssetCode,
                  fixedAssetName: this.FixedAsset.fixedAssetName,
                  departmentId: this.FixedAsset.departmentId,
                  fixedAssetCategoryId: this.FixedAsset.fixedAssetCategoryId,
                  purchaseDate: this.FixedAsset.purchaseDate,
                  cost: this.FixedAsset.cost,
                  quantity: this.FixedAsset.quantity,
                  depreciationRate: this.FixedAsset.depreciationRate,
                  trackedYear: this.FixedAsset.trackedYear,
                  lifeTime: this.lifeTime,
                  productionYear: this.productionYear,
                  valueAtrophyYear: this.valueAtrophyYear,
                  createdBy: "string",
                  createdDate: "2022-12-26T17:50:33.580Z",
                  modifiedBy: "string",
                  modifiedDate: "2022-12-26T17:50:33.580Z",
                })
                .then((res) => {
                  console.log(res);
                });
              console.log(this.FixedAsset);
              this.$emit("onShowToastSuccess");
              this.canceForm();
              break;
          }
        }
      } catch (error) {
        console.log(error);
      }
    },

    //L???y t??n b??? ph???n s??? d???ng b???i id b??? ph???n s??? d???ng
    getPartNameByDepartmentId(departmentId) {
      for (const department of this.Departments) {
        if (department.departmentId == departmentId)
          return department.departmentName;
      }
    },

    //L???y m?? b??? ph???n s??? d???ng b???i id b??? ph???n s??? d???ng
    getPartCodeByDepartmentId(departmentId) {
      for (const department of this.Departments) {
        if (department.departmentId == departmentId)
          return department.departmentCode;
      }
    },

    //L???y t??n lo???i t??i s???n b???i id lo???i t??i s???n
    getTypeAssetNameByFixedAssetCategoryId(fixedAssetCategoryId) {
      for (const assetCategory of this.AssetCategorys) {
        if (assetCategory.fixedAssetCategoryId == fixedAssetCategoryId)
          return assetCategory.fixedAssetCategoryName;
      }
    },

    //L???y m?? lo???i t??i s???n b???i id lo???i t??i s???n
    getTypeAssetCodeByFixedAssetCategoryId(fixedAssetCategoryId) {
      for (const assetCategory of this.AssetCategorys) {
        if (assetCategory.fixedAssetCategoryId == fixedAssetCategoryId)
          return assetCategory.fixedAssetCategoryCode;
      }
    },

    //L???y s??? n??m s??? d???ng b??? m?? lo???i t??i s???n
    getNumberYearOfUseByTypeAssetCode(TypeAssetCode) {
      for (const typeAsset of this.AssetCategorys) {
        if (typeAsset.TypeAssetCode == TypeAssetCode)
          return typeAsset.NumberYearOfUse;
      }
    },

    //L???y t??? l??? hao m??n d???a tr??n m?? lo???i t??i s???n
    getWearRateByTypeAssetCode(TypeAssetCode) {
      for (const typeAsset of this.AssetCategorys) {
        if (typeAsset.TypeAssetCode == TypeAssetCode) return typeAsset.WearRate;
      }
    },

    //Truy???n gi?? tr??? t??i s???n ban ?????u v??o form
    CreateFormUpdate(updateAsset) {
      this.FixedAsset = updateAsset;
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,400;1,300;1,400;1,500&display=swap");
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

.position-relative {
  position: relative;
}

.cl-red {
  color: red;
  position: relative;
  top: 3px;
}

.bg-ccc {
  background-color: rgb(225, 225, 225);
}

.black-ground {
  display: inline-block;
  box-sizing: border-box;
  position: fixed;
  background-color: rgba(0, 0, 0, 0.5);
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  z-index: 10;
}

.form-add {
  box-sizing: border-box;
  width: 1000px;
  height: 622px;
  top: calc((100vh - 622px) / 2);
  bottom: calc((100vh - 622px) / 2);
  right: calc((100vw - 1000px) / 2);
  left: calc((100vw - 1000px) / 2);
  background-color: #ffffff;
  position: fixed;
  z-index: 100;
  border-radius: 6px;
  overflow: hidden;
}

.form-add__container {
  width: 100%;
  height: calc(100% - 108px);
  box-sizing: border-box;
  margin-top: 24px;
}

.form-add__container__head {
  display: flex;
  justify-content: space-between;
  margin-left: 24px;
  margin-right: 24px;
}

.form-add__container__head__title {
  font-size: 20px;
  font-weight: 600;
}

.form-add__container__head .icon-close {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -375px -287px;
  width: 18px;
  height: 18px;
  cursor: pointer;
}

.form-add__container__body {
  margin-top: 24px;
  margin-left: 24px;
  margin-right: 24px;
  margin-bottom: 13px;
}

.form-row {
  display: flex;
  justify-content: space-between;
}

.col1-3 {
  display: flex;
  flex-direction: column;
  width: calc(100% / 3 - 12px);
}

.col2-3 {
  display: flex;
  flex-direction: column;
  width: calc(2 * 100% / 3 - 12px);
}

.col1-3-center {
  width: calc(100% / 3 - 23px) !important;
}

.form-row label {
  font-size: 14px;
  font-weight: 500;
}

.form-row .form-control {
  height: 36px;
  margin-top: 8px;
  padding-left: 8px;
  padding-right: 10px;
  font-size: 14px;
  font-family: "Roboto", sans-serif;
  font-weight: 400;
  border: 1px solid #b3b3b3;
  border-radius: 3px;
  outline: none;
  width: 100%;
}

.form-control:hover {
  border-color: #1aa4c8;
}

.col-select {
  position: relative;
  margin-top: 8px;
  border: 1px solid #b3b3b3;
  font-size: 14px;
  font-style: italic;
  font-family: "Roboto", sans-serif;
  font-weight: 400;
  border-radius: 3px;
  display: flex;
  width: 100%;
}

.col-select:hover {
  border-color: #1aa4c8;
}

.option-filter__content {
  padding-left: 8px;
  padding-right: 4px;
  flex: 1;
  height: 36px;
  border: none;
  outline: none;
  cursor: context-menu;
}

.select-option.icon-down {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -62px -330px;
  width: 20px;
  height: 20px;
  margin: 7px;
}

.option-filter-items {
  position: absolute;
  margin-top: 3px;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
  z-index: 10;
  width: 100%;
  left: -1px;
  top: 34px;
  max-height: 180px;
  overflow-y: scroll;
  overflow-x: hidden;
}

.option-filter-item {
  display: none;
  background-color: #ffffff;
  width: calc(100% + 2px);
  height: 36px;
  font-size: 14px;
  line-height: 34px;
  padding-left: 6px;
  font-style: normal;
}

.option-filter-item:hover {
  background-color: rgb(176, 219, 229);
  cursor: pointer;
}

.date-picker {
  margin-top: 8px;
}

.icon-datepicker {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -287px -67px;
  width: 18px;
  height: 18px;
  position: relative;
  top: -26px;
  right: -276px;
}

.form-add__container__foot {
  width: 100%;
  height: 82px;
  background-color: rgb(227, 227, 227);
  display: flex;
  flex-direction: row-reverse;
}

.form-add__container__foot .btn-submit {
  width: 100px;
  height: 36px;
  margin: 12px 24px 12px 12px;
  line-height: 36px;
  text-align: center;
  background-color: #1aa4c8;
  border-radius: 2px;
  color: #ffffff;
  cursor: pointer;
}

.form-add__container__foot .btn-cancel {
  width: 100px;
  height: 36px;
  margin: 12px 0 12px 12px;
  line-height: 36px;
  text-align: center;
  background-color: #fff;
  border-radius: 2px;
  color: #000;
  cursor: pointer;
}

.form-group {
}

.form-group .form-message {
  font-size: 13px;
  height: 16px;
  line-height: 16px;
  color: #f33a58;
}

.form-group.invalid .form-control {
  border-color: #f33a58;
}

.text-align-right {
  text-align: right;
}

.padding-right-20 {
  padding-right: 20px !important;
}

input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
}

.out-ip {
  display: flex;
}

::-webkit-scrollbar {
  width: 5px;
}

::-webkit-scrollbar-track {
  background: #f4f7ff;
  border-radius: 5px;
}

::-webkit-scrollbar-thumb {
  background: #ccc;
}
</style>


