<template>
  <div>
    <div class="content__option">
      <div class="content__option__left">
        <div class="option-search">
          <div class="icon-search"></div>
          <input
            @keydown="filterByAssetName"
            type="text"
            ref="ipSearch"
            :placeholder="Placeholder.SearchAsset"
          />
        </div>
        <div class="option-filter">
          <div class="icon-filter"></div>
          <input
            @click="showOption($event)"
            class="option-filter__content"
            value=""
            :placeholder="Placeholder.CategoryAsset"
            readonly
          />
          <div class="icon-down" @click="showOption($event)"></div>
          <div class="option-filter-items">
            <div
              class="option-filter-item"
              ref="optionFilteritem"
              v-for="(AssetCategory, index) in AssetCategorys"
              :key="index"
              @click="
                changeOption(
                  $event,
                  AssetCategory.fixedAssetCategoryName,
                  AssetCategory.fixedAssetCategoryId,
                  'AssetCategorys'
                )
              "
            >
              {{ AssetCategory.fixedAssetCategoryName }}
            </div>
          </div>
        </div>
        <div class="option-filter">
          <div class="icon-filter"></div>
          <input
            @click="showOption($event)"
            class="option-filter__content"
            :placeholder="Placeholder.Department"
            readonly
          />
          <div class="icon-down" @click="showOption($event)"></div>
          <div class="option-filter-items">
            <div
              class="option-filter-item"
              ref="optionFilteritem"
              v-for="(part, index) in Departments"
              :key="index"
              @click="
                changeOption(
                  $event,
                  part.departmentName,
                  part.departmentId,
                  'Departments'
                )
              "
            >
              {{ part.departmentName }}
            </div>
          </div>
        </div>
      </div>
      <div class="content__option__right">
        <div class="content__option__right__btn-add-asset" @click="ShowFormAdd">
          {{ Title.AddAsset }}
        </div>
        <div class="content__option__right_btn">
          <div class="icon-updt" :title="Tooltip.ExportTooltip"></div>
        </div>
        <div class="content__option__right_btn">
          <div
            id="delete"
            class="icon-deleteRed"
            @click="checkDeleteData"
            :title="Tooltip.DeleteTooltip"
          ></div>
        </div>
      </div>
    </div>
    <div class="content__table">
      <table>
        <thead>
          <tr class="table-start">
            <th class="ta-left">
              <input class="ip-checkbox" type="checkbox" ref="checkFull" />
            </th>
            <th class="ta-center w32" :title="Tooltip.SerialTooltip">
              {{ ContentText.Serial }}
            </th>
            <th class="ta-left w130">{{ ContentText.FixedAssetCode }}</th>
            <th class="ta-left w200">{{ ContentText.FixedAssetName }}</th>
            <th class="ta-left w130">
              {{ ContentText.FixedAssetCategoryName }}
            </th>
            <th class="ta-left w180">{{ ContentText.Department }}</th>
            <th class="ta-right w70">{{ ContentText.Quantity }}</th>
            <th class="ta-right w100">{{ ContentText.Cost }}</th>
            <th class="ta-right w100" :title="Tooltip.AccumulatedTooltip">
              {{ ContentText.Accumulated }}
            </th>
            <th class="ta-right w100">{{ ContentText.ResidualValue }}</th>
            <th class="w100">{{ ContentText.Feature }}</th>
          </tr>
        </thead>

        <tbody>
          <tr
            class="table-start"
            v-for="(asset, index) in assets"
            :key="index"
            @dblclick="ShowFormUpdate(asset)"
          >
            <td class="ta-left">
              <input
                :data="asset.fixedAssetId"
                ref="ipCheckbox"
                class="ip-checkbox"
                type="checkbox"
                @change="changeInputCheckBox"
              />
            </td>
            <td id="stt" class="ta-center w32">{{ index + 1 }}</td>
            <td class="ta-left w130 line-clamp">{{ asset.fixedAssetCode }}</td>
            <td class="ta-left w200 line-clamp">{{ asset.fixedAssetName }}</td>
            <td class="ta-left w130 line-clamp">
              {{ getNameAssetCategory(asset.fixedAssetCategoryId) }}
            </td>
            <td class="ta-left w180 line-clamp">
              {{ getNamePart(asset.departmentId) }}
            </td>
            <td class="ta-right w70 line-clamp">{{ asset.quantity }}</td>
            <td class="ta-right w100 line-clamp">
              {{ formatCash(asset.cost.toString()) }}
            </td>
            <td class="ta-right w100 line-clamp">
              {{
                formatCash(
                  getAccumulated(asset.cost, asset.depreciationRate).toString()
                )
              }}
            </td>
            <td class="ta-right w100 line-clamp">
              {{
                formatCash(
                  getResidualValue(
                    asset.cost,
                    asset.depreciationRate
                  ).toString()
                )
              }}
            </td>
            <td class="w100">
              <div class="icon-update"></div>
              <div class="icon-delete"></div>
            </td>
          </tr>
        </tbody>

        <tfoot>
          <tr class="table-start">
            <td class="ta-center total-records">
              {{ ContentText.Sum }}<b>{{ totalRecord }}</b>
              {{ ContentText.Record }}
            </td>
            <td class="number-records">
              <div class="number-record">{{ this.APIAsset.limit }}</div>
              <div class="icon-down"></div>
            </td>
            <td class="number-of-pages">
              <div class="number-of-page icon-prew"></div>
              <div
                class="number-of-page pageNode"
                v-for="(page, index) in totalPage"
                :key="index"
                @click="changePage($event, page)"
                ref="paging"
              >
                {{ page }}
              </div>
              <div class="number-of-page icon-next"></div>
            </td>
            <!-- <td class="w-auto"></td> -->
            <td class="ta-right w70">{{ sumQuantity }}</td>
            <td class="ta-right w100">
              {{ formatCash(sumCost.toString()) }}
            </td>
            <td class="ta-right w100">
              {{ formatCash(sumAccumulated.toString()) }}
            </td>
            <td class="ta-right w100">
              {{ formatCash(sumResidualValue.toString()) }}
            </td>
            <td class="w100"></td>
          </tr>
        </tfoot>
      </table>
    </div>
    <form-mode
      @onShowToastSuccess="showToastSuccess"
      v-if="isShowForm"
      :mode="mode"
      :updateAsset="updateAsset"
      @hide-Form="hideForm()"
    />
    <dialogDelete
      @onDeleteConfirm="deleteData"
      @onUnDeleteConfirm="closeDialogDelete"
      v-if="isShowDialogDelete"
    />
    <dialogLoading v-if="isShowDialogLoading" />
    <toastSuccess v-if="isShowToastSuccess" />
    <toastDelete v-if="isShowToastDelete" />
  </div>
</template>
<script>
import axios from "axios";
import formMode from "../popup/formMode.vue";
import Enum from "@/js/enum.js";
import Resource from "@/js/resource.js";
import toastSuccess from "@/components/toast-message/toastSuccess.vue";
import dialogDelete from "@/components/dialog/dialogDelete.vue";
import dialogLoading from "@/components/dialog/dialogLoading.vue";
import toastDelete from "@/components/toast-message/toastDelete.vue";
import { APIAsset } from "@/class/APIAsset.js";

export default {
  components: {
    formMode,
    toastSuccess,
    dialogDelete,
    dialogLoading,
    toastDelete,
  },
  name: "TheMain",
  data() {
    return {
      mode: 0, // mode của form
      ContentText: Resource.ContentText, //Table content gọi từ file resource.js
      Tooltip: Resource.Tooltip, //Tooltip gọi từ file resource.js
      Placeholder: Resource.Placeholder, //Placeholder gọi từ file resource.js
      Title: Resource.Title, //Tiêu đề gọi từ file resource.js
      updateAsset: {}, // tài sản truyền vào form cập nhật tài sản
      assets: [], // Danh sách tài sản lấy từ API
      AssetCategorys: [], // Danh sách loại tìa sản lấy từ API
      Departments: [], // Danh sách bộ phận sử dụng lấy từ API
      isShowForm: false, // Ẩn hiện form
      APIAsset: new APIAsset(), // Đối tượng truyền vài api tài sản
      totalRecord: 0, // Tổng ố bản ghi bị ảnh hưởng
      isShowToastSuccess: false,
      isShowToastDelete: false,
      isShowDialogDelete: false,
      isShowDialogLoading: true,
      assetSelected: {}, //
      APIFixedAsset: "", // chuỗi API lấy tài sản
      APIDepartment: "", // chuỗi API lấy danh sách phòng ban
      APIFixedAssetCategory: "", // chuỗi API lấy danh sách loại tài sản
      totalPage: 0, // Tổng số trang
      currenPage: 1, // Trang hiện tại đang xuất hiện
    };
  },
  created() {
    var _this = this;

    // Sự kiện khi click ra ngoài thì đóng thẻ select
    window.addEventListener("click", function () {
      try {
        // Chon tất cả thẻ option
        let listSelectControl = _this.$refs.optionFilteritem;

        // Ẩn hết toàn bộ thẻ select đang mở
        for (const selectControl of Array.from(listSelectControl)) {
          selectControl.style.display = "none";
        }
      } catch (error) {
        console.log(error);
      }
    });

    // Cài đặt API lấy tài sản
    this.APIFixedAsset =
      "http://localhost:63515/api/Assets/filter?keyword=" +
      this.APIAsset.keyword +
      "&limit=" +
      this.APIAsset.limit +
      "&offset=" +
      this.APIAsset.offset +
      "&departmentId=" +
      this.APIAsset.departmentId +
      "&fixedAssetCategoryId=" +
      this.APIAsset.fixedAssetCategoryId;

    // Cài đặt API lấy danh sách phòng ban
    this.APIDepartment = "http://localhost:63515/api/Departments";

    // Cài đặt API lấy danh sách loại tài sản
    this.APIFixedAssetCategory = "http://localhost:63515/api/AssetCategorys";

    // Load dữ liệu trang
    this.loadData();
  },
  updated() {
    // Custom paging khi trang hiện tại là trang đầu tiên (currenPage = 1)
    if (this.currenPage == 1) {
      let pages = document.querySelectorAll(".pageNode");

      // Thêm class active cho trang hiện tại (tô đậm)
      pages[0].classList.add("number-of-page-active");

      // Xóa toàn bộ element ...
      let listnode = document.querySelectorAll(".dotNode");
      if (listnode) {
        for (let node of listnode) {
          node.remove();
        }
      }

      // custom paging (xóa những trang ko liền kề đầu, cuối, currentPage)
      if (this.totalPage > 3) {
        for (let i = 2; i < this.totalPage - 1; i++) {
          pages[i].style.display = "none";
        }

        let html = '<div class="number-of-page dotNode">...</div>';
        pages[1].insertAdjacentHTML("afterend", html);
      }
    }

    
  },
  methods: {
    // Cập nhật API lấy tài sản
    getAPIFixedAsset() {
      return (
        "http://localhost:63515/api/Assets/filter?keyword=" +
        this.APIAsset.keyword +
        "&limit=" +
        this.APIAsset.limit +
        "&offset=" +
        this.APIAsset.offset +
        "&departmentId=" +
        this.APIAsset.departmentId +
        "&fixedAssetCategoryId=" +
        this.APIAsset.fixedAssetCategoryId
      );
    },

    // Cập nhật API lấy danh sách phòng ban
    getAPIDepartment() {
      return "http://localhost:63515/api/Departments";
    },
    // Cập nhật API lấy danh sách loại tài sản
    getAPIFixedAssetCategory() {
      return "http://localhost:63515/api/AssetCategorys";
    },

    /**
     * Hàm format tiền str: String
     * Tạo bởi: NVAn(20/12/2022)
     */
    
    /**
     * 
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
     * Hàm Load data
     * Tạo bởi: NVAn(20/12/2022)
     */
    loadData() {
      // load danh sách tài sản
      this.isShowDialogLoading = true;

      try {
        fetch(this.getAPIFixedAsset())
          .then((response) => {
            return response.json();
          })
          .then((asset) => {
            // Cập nhật danh sách tài sản trả về trong trang
            this.assets = asset.data;

            // Cập nhật danh sách sô bản ghi bị ảnh hưởng
            this.totalRecord = asset.totalRecord;

            //Cập nhật số trang = số bản ghi bị ảnh hưởng / số tài sản trong 1 trang
            this.totalPage = Math.floor(asset.totalRecord / this.APIAsset.limit);

            this.isShowDialogLoading = false;
          });
      } catch (error) {
        console.log(error);
      }

      // load danh sách loại tài sản
      try {
        fetch(this.getAPIFixedAssetCategory())
          .then((response) => {
            return response.json();
          })
          .then((AssetCategory) => {
            this.AssetCategorys = AssetCategory;
          });
      } catch (error) {
        console.log(error);
      }

      // load danh sách phòng ban
      try {
        fetch(this.getAPIDepartment())
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
     * Hàm hàm lấy ra tên loại tài sản tương ứng với mã tài sản truyền vào
     * Tạo bởi: NVAn(20/12/2022)
     */
    getNameAssetCategory(fixedAssetCategoryId) {
      for (const AssetCategory of this.AssetCategorys) {
        if (AssetCategory.fixedAssetCategoryId === fixedAssetCategoryId) {
          return AssetCategory.fixedAssetCategoryName;
        }
      }
    },

    /**
     * hàm lấy ra tên bộ phận sử dụng với mã bộ phận sử dụng truyền vào
     * Tạo bởi: NVAn(20/12/2022)
     */
    getNamePart(departmentId) {
      for (const Part of this.Departments) {
        if (Part.departmentId === departmentId) {
          return Part.departmentName;
        }
      }
    },

    /**
     * hàm tính lũy kế = nguyên giá * tỷ lệ hao mòn năm
     * Tạo bởi: NVAn(20/12/2022)
     */
    getAccumulated(cost, depreciationRate) {
      return Math.round((cost * depreciationRate) / 100);
    },

    /**
     * hàm tính giá trị giá trị còn lại = nguyên giá - lũy kế
     * Tạo bởi: NVAn(20/12/2022)
     */
    getResidualValue(cost, depreciationRate) {
      return cost - this.getAccumulated(cost, depreciationRate);
    },

    /**
     * Bât tắt form khi đóng mở thêm mới
     * Tạo bởi: NVAn(20/12/2022)
     */
    ShowFormAdd() {
      this.isShowForm = true;
      this.mode = Enum.Mode.Add;
    },

    /**
     * Xử lý sự kiện khi click vào từng hàng để sửa thông tin
     * Tạo bởi: NVAn(20/12/2022)
     */
    ShowFormUpdate(asset) {
      try {
        this.isShowForm = !this.isShowForm;
        this.mode = Enum.Mode.Update;
        this.updateAsset = asset;
      } catch (error) {
        console.log(error);
      }
    },

    /**
     * Sự kiện tạo thẻ select(blur và ra sẽ hiện option)
     * Tạo bởi: NVAn(20/12/2022)
     */
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
     * Hiện toast success khi thêm mới hoặc cập nhật
     * Tạo bởi: NVAn(20/12/2022)
     */
    showToastSuccess() {
      this.isShowToastSuccess = true;

      setTimeout(() => {
        this.isShowToastSuccess = false;
      }, 3000);
    },

    /**
     * Hiện toast delete khi thêm xóa thành công
     * Tạo bởi: NVAn(20/12/2022)
     */
    showToastDelete() {
      this.isShowToastDelete = true;

      setTimeout(() => {
        this.isShowToastDelete = false;
      }, 3000);
    },

    /**
     * Xử lý sự kiện khi thay đổi option thẻ select
     * Tạo bởi: NVAn(20/12/2022)
     */
    changeOption(e, value, id, type) {
      e.path[2].querySelector(".option-filter__content").value = value;
      if (type == "AssetCategorys") {
        this.APIAsset.fixedAssetCategoryId = id;
      } else if (type == "Departments") {
        this.APIAsset.departmentId = id;
      }

      this.loadData();
    },

    /**
     * Xác nhận xóa bản ghi: gọi dialog delete
     * Tạo bởi: NVAn(20/12/2022)
     */
    checkDeleteData() {
      this.isShowDialogDelete = true;
    },

    /**
     * Hàm ẩn Form
     * Tạo bởi: NVAn(20/12/2022)
     */
    hideForm() {
      this.isShowForm = false;
    },

    /**
     * Xóa bản ghi (xóa những ô được tích)
     * Tạo bởi: NVAn(20/12/2022)
     */
    deleteData() {
      let listIpCheckbox = this.$refs.ipCheckbox;
      for (const ipCheckbox of listIpCheckbox) {
        if (ipCheckbox.checked) {
          axios
            .delete(
              `http://localhost:63515/api/Assets/${ipCheckbox.attributes.data.value}`,
              {}
            )
            .then((res) => {
              console.log(res);
              this.showToastDelete();
              ipCheckbox.checked = false;
            });
        }
      }
      setTimeout(() => {
        this.loadData();
      }, 2000);

      this.isShowDialogDelete = false;
    },

    /**
     * Đóng dialog
     * Tạo bởi: NVAn(20/12/2022)
     */
    closeDialogDelete() {
      this.isShowDialogDelete = false;
    },

    /**
     * Bắt sự kiện khi ấn enter ở search
     * Tạo bởi: NVAn(3/1/2023)
     */
    filterByAssetName(e) {
      var ipSearch = this.$refs.ipSearch;

      if (e.keyCode === Enum.KeyCode.ENTER) {
        this.APIAsset.keyword = ipSearch.value;
        this.loadData();
      }
    },
    
    // Bắt sự kiện khi thay đổi page
    changePage(e, page) {
      let pages = this.$refs.paging;

      // Đạt lại trang hiện tại
      this.currenPage = page;

      // Load lại page phù hợp
      this.APIAsset.offset = this.currenPage * this.APIAsset.limit;
      this.loadData();

      // Xóa class active ở tất cả các trang
      for (let i = 0; i < this.totalPage; i++) {
        pages[i].classList.remove("number-of-page-active");
      }

      // Thêm class active cho trang hiện tại (tô đậm)
      e.path[0].classList.add("number-of-page-active");

      // Hiện lại tất cả paging
      for (let i = 0; i < this.totalPage; i++) {
        pages[i].style.display = "inline-block";
      }

      // Xóa toàn bộ element ...
      let listnode = document.querySelectorAll(".dotNode");
      if (listnode) {
        for (let node of listnode) {
          node.remove();
        }
      }

      // custom paging (xóa những trang ko liền kề đầu, cuối, currentPage)
      if (this.currenPage - 1 > 2) {
        for (let i = 1; i < this.currenPage - 2; i++) {
          pages[i].style.display = "none";
        }

        let html = '<div class="number-of-page dotNode">...</div>';
        pages[0].insertAdjacentHTML("afterend", html);
      }
      if (this.totalPage - this.currenPage > 2) {
        for (let i = this.totalPage - 2; i > this.currenPage; i--) {
          pages[i].style.display = "none";
        }

        let html = '<div class="number-of-page dotNode">...</div>';
        pages[this.totalPage - 1].insertAdjacentHTML("beforebegin", html);
      }
    },

    // Bắt sự kiện khi có sự thay đổi tại ô check box
    changeInputCheckBox(){
      let listIpCheckbox = this.$refs.ipCheckbox;

      // Kiểm tra từng thằng input checkBox, thẻ nào true thì thêm class active-checked và ngược lại
      for (const ipCheckbox of listIpCheckbox) {
        if (ipCheckbox.checked) {
          ipCheckbox.parentElement.parentElement.classList.add("active-checked");
        }else{
          ipCheckbox.parentElement.parentElement.classList.remove("active-checked");
        }
      }
    }
  },
  computed: {
    /**
     * Tổng số lượng
     * Tạo bởi: NVAn(20/12/2022)
     */
    sumQuantity() {
      return this.assets.reduce((total, asset) => total + asset.quantity, 0);
    },

    /**
     * Tổng nguyên giá
     * Tạo bởi: NVAn(20/12/2022)
     */
    sumCost() {
      return this.assets.reduce((total, asset) => total + asset.cost, 0);
    },

    /**
     * Tổng lũy kế
     * Tạo bởi: NVAn(20/12/2022)getAccumulated(cost, fixedAssetCategoryId)
     */
    sumAccumulated() {
      return Math.round(
        this.assets.reduce(
          (total, asset) =>
            total + this.getAccumulated(asset.cost, asset.depreciationRate),
          0
        )
      );
    },

    /**
     * Tổng giá trị còn lại
     * Tạo bởi: NVAn(20/12/2022)
     */
    sumResidualValue() {
      return this.assets.reduce(
        (total, asset) =>
          total + this.getResidualValue(asset.cost, asset.depreciationRate),
        0
      );
    },
  },
};
</script>
<style scoped>
:root {
  --bg-color: rgb(176, 219, 229);
}

* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

.content__option {
  display: flex;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 16px;
}

.content__option__left {
  display: flex;
}

.option-search {
  background-color: #fff;
  width: 180px;
  height: 36px;
  border-radius: 4px;
  border: 1px solid #afafaf;
  display: flex;
}

.icon-search {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -243px -23px;
  width: 18px;
  height: 18px;
  margin: 9px;
}

.option-search input {
  border: none;
  outline: none;
  width: 140px;
  font-style: normal;
}

.option-search input::placeholder {
  font-style: italic;
}

.option-filter {
  background-color: #fff;
  width: 220px;
  height: 36px;
  border-radius: 4px;
  border: 1px solid #afafaf;
  margin-left: 12px;
  display: flex;
  position: relative;
}

.option-filter .icon-filter {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -243px -69px;
  width: 18px;
  height: 18px;
  margin: 9px;
}

select {
  border: none;
  outline: none;
  width: 174px;
  font-weight: 500;
}

.option-filter__content {
  flex: 1;
  line-height: 34px;
  font-size: 14px;
  max-width: 148px;
  cursor: context-menu;
  border: none;
  outline: none;
  cursor: auto;
}

.option-filter .icon-down {
  background: url("../../assets/icon/qlts-icon.png") no-repeat -62px -330px;
  width: 20px;
  height: 20px;
  margin: 7px;
}

.option-filter-items {
  position: absolute;
  margin-top: 2px;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
  z-index: 1;
  width: 100%;
  top: 34px;
  max-height: 180px;
  overflow-y: scroll;
  overflow-x: hidden;
}

.option-filter-item {
  display: none;
  background-color: #ffffff;
  width: 100%;
  height: 34px;
  font-size: 14px;
  line-height: 30px;
  padding-left: 36px;
}

.option-filter-item:hover {
  background-color: rgb(176, 219, 229);
  cursor: pointer;
}

.content__option__right {
  display: flex;
}

.content__option__right__btn-add-asset {
  width: 120px;
  height: 36px;
  line-height: 36px;
  font-size: 14px;
  color: #fff;
  padding-left: 14px;
  background-color: #1aa4c8;
  border-radius: 3px;
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.16);
}

.content__option__right__btn-add-asset:hover {
  cursor: pointer;
  background-color: #66afff;
}

.content__option__right_btn {
  background-color: #ffffff;
  border-radius: 3px;
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.16);
  width: 36px;
  height: 36px;
  overflow: hidden;
  margin-left: 10px;
}

.content__option__right_btn:hover {
  cursor: pointer;
}

.icon-updt {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -287px -111px;
  width: 18px;
  height: 18px;
  margin: 9px;
}

.icon-deleteRed {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -463px -111px;
  width: 18px;
  height: 18px;
  margin: 9px;
  cursor: pointer;
}

.icon-deleteRed:hover {
  opacity: 0.9;
}

.icon-update {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -156px -68px;
  width: 16px;
  height: 16px;
  cursor: pointer;
}

.w100 .icon-update {
  margin-left: 18px;
}

.w100 .icon-update:hover {
}

.icon-delete {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -463px -111px;
  width: 18px;
  height: 18px;
  cursor: pointer;
}

.w100 .icon-delete {
  margin-right: 18px;
}

.w100 .icon-delete:hover {
}

/* Table  */

.content__table {
  height: calc(100vh - 158px);
  overflow: auto;
  background-color: #ffffff;
  margin-right: 20px;
}

::-webkit-scrollbar {
  width: 3px;
}

::-webkit-scrollbar-track {
  background: #f4f7ff;
}

::-webkit-scrollbar-thumb {
  background: #ccc;
}

.content__table table {
  width: 100%;
  height: calc(100vh - 158px);
}

.table-start {
  width: 100%;
  font-size: 14px;
  height: 38px;
  padding: 9px 0 0px;
  display: flex;
  justify-content: space-between;
}

thead {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
}

thead .table-start {
  background-color: #dddddd;
  border-top-left-radius: 4px;
  border-top-right-radius: 4px;
}

tbody .table-start {
  border-top: none;
}

tbody {
  height: calc(100vh - 242px);
}

tbody tr:hover {
  background-color: rgb(176, 219, 229) !important;
}

tfoot {
  position: -webkit-sticky;
  position: sticky;
  bottom: 0;
}

tfoot .table-start {
  height: 40px;
  background-color: #ffffff;
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
  justify-content: flex-start;
}

.table-start {
  border: 1px solid #ccc;
}

.ip-checkbox {
  margin-left: 10px;
}

.ta-left {
  text-align: left;
}

.ta-center {
  text-align: center;
}

.ta-right {
  text-align: right;
}

.w32 {
  width: 32px;
}

.w130 {
  width: 130px;
}

.w200 {
  width: 200px;
}

.w130 {
  width: 130px;
}

.w180 {
  width: 180px;
}

.w70 {
  width: 70px;
}

.w100 {
  width: 100px;
}

.w100 {
  width: 100px;
  display: flex;
  justify-content: space-around;
}

.active-checked{
  background-color: rgb(176, 219, 229) !important;
}

.total-records {
  width: 150px;
}

.total-records b {
  color: #4f4f4f;
}

.line-clamp {
  display: inline-block;
  max-width: 100%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.table-start .number-records {
  width: 60px;
  height: 24px;
  display: flex;
  justify-content: space-around;
  border: 1px solid #ccc;
  border-radius: 2px;
  position: relative;
  top: -2px;
  margin-left: 20px;
  margin-right: 20px;
}

.number-records > .icon-down {
  background: url(http://localhost:8080/img/qlts-icon.c1b7328e.svg) no-repeat -72px -337px;
  width: 8px;
  height: 8px;
  margin-top: 7px;
}

.number-of-pages {
  display: flex;
  justify-content: space-between;
  /* width: 540px; */
}

.number-of-page {
  width: 20px;
  height: 20px;
  margin: 0 5px;
  text-align: center;
  cursor: pointer;
}

.number-of-page-active {
  font-weight: 500;
}

.number-of-page.icon-prew {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -29px -242px;
  width: 7px;
  height: 20px;
}

.number-of-page.icon-next {
  background: url("../../assets/icon/qlts-icon.svg") no-repeat -73px -242px;
  width: 7px;
  height: 20px;
}

.w-auto {
  width: 395px;
}
</style>
