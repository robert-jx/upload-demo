<template>
  <div class="home">
    <div class="content">
      <public-upload
        :fieldsList="fieldsList"
        @getFileData="getFileData"
      ></public-upload>
    </div>
  </div>
</template>

<script>
import publicUpload from "@/components/public-upload";
const fieldsList = {
  desc: "描述",
  type: "点类型",
  address: "地址",
  point: "点位",
  camera: "联动摄像机",
};
export default {
  name: "home",
  components: { publicUpload },
  data() {
    return { fieldsList };
  },
  methods: {
    getFileData(e) {
      let list = JSON.parse(e);
      console.log("list===========", list);
    },
  },
};
</script>

<style lang="scss" scoped>
.home {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #202020;
  .content {
    width: 600px;
    height: 300px;
  }
}
</style>