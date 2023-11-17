<template>
  <div class="public-upload">
    <el-upload
      class="upload-demo"
      drag
      action=""
      multiple
      :limit="1"
      :show-file-list="false"
      :on-change="readExcel"
      :auto-upload="false"
    >
      <div class="upload-class">
        <div class="upload-icon">
          <i class="el-icon-cloudy"></i>
        </div>
        <div class="upload-text">
          下载模板并完善信息后，可将文件拖拽到此处导入
        </div>
        <div class="upload-desc">
          文件支持xlsx、xls导入格式，最大可导入{{ maxSize }}M文件
        </div>
      </div>
    </el-upload>
  </div>
</template>

<script>
import * as XLSX from "xlsx";
export default {
  name: "public-upload",
  props: {
    fieldsList: {
      type: Object,
      default: () => {},
    },
    maxSize: {
      type: Number,
      default: 10,
    },
  },
  data() {
    return {};
  },
  created() {},
  methods: {
    //导入 表单上传
    readExcel(file) {
      const types = file.name.slice(file.name.lastIndexOf("."));
      const fileType = [".xlsx", ".xls"].some((item) => item === types);
      const maxSize = this.maxSize * 1024 * 1000;
      //  校验格式
      if (!fileType) {
        this.$message("格式错误！请重新上传");
        return;
      }
      if (file.size > maxSize) {
        this.$message("文件过大！请重新上传");
        return;
      }
      // 返回workbook
      this.file2Xce(file).then((tabJson) => {
        this.$emit("getFileData", JSON.stringify(tabJson));
        this.processingData(tabJson); //处理导入的数据使导入的数据在页面中展示。这个方法根据自己的需求，属于定制化
      });
    },

    // 读表单，返回workbook
    file2Xce(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => {
          const data = e.target.result;
          // 二进制流方式读取得到整份Excel表格对象
          this.excelData = XLSX.read(data, {
            type: "binary",
          });
          // 只取第一个工作表
          const wsname = this.excelData.SheetNames[0]; // 取第一张表
          const ws = XLSX.utils.sheet_to_json(this.excelData.Sheets[wsname]); // 生成json表格内容
          resolve(ws);
        };
        reader.readAsBinaryString(file.raw); //file.raw取上传文件的File
      });
    },
    processingData(item) {
      let list = [];
      item?.map((v) => {
        let obj = {};
        Object.keys(this.fieldsList)?.forEach((key) => {
          obj[key] = v[this.fieldsList[key]];
        });
        list.push(obj);
      });
      this.$emit("getFileData", JSON.stringify(list));
    },
  },
};
</script>

<style lang="scss" scoped>
.public-upload {
  width: 100%;
  height: 100%;
  background: #303030;
  .upload-class {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    align-items: center;
    justify-content: center;
    .upload-icon {
      font-size: 67px;
      line-height: 70px;
      color: #fff;
    }
    .upload-text {
      font-weight: 600;
      font-size: 14px;
      line-height: 20px;
      color: #fff;
    }
    .upload-desc {
      font-size: 12px;
      line-height: 20px;
      color: #959cb0;
    }
  }
  &::v-deep .el-upload {
    width: 100%;
    height: 100%;
  }
  &::v-deep .upload-demo {
    width: 100%;
    height: 100%;
  }
  &::v-deep .el-upload-dragger {
    background: #303030;
    border: 1px dashed #424242;
    border-radius: 6px;
    box-sizing: border-box;
    width: 100%;
    height: 100%;
    text-align: center;
    cursor: pointer;
    overflow: hidden;
  }
}
</style>