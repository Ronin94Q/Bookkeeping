<template>
  <div class="contents">
    <el-input style="width:200px;margin-right:20px" v-model="cityName"></el-input>
    <el-button type="primary" @click="search">搜 索</el-button>

    <div class="cityContent">
      <ul>
        <li v-for="(item,index) in allCity" :key="index" @click="godetail(item.id)">{{item.name}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { getAllCity } from "@/network/api";
export default {
  props: {},
  data() {
    return {
      cityName: "",
      allCity: []
    };
  },
  components: {},
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.getAllCity(this.cityName);
  },
  methods: {
    getAllCity(cityName) {
      getAllCity(`?cityName=${cityName}`).then(res => {
        if (res.data.content) {
          this.allCity = res.data.content;
        } else {
          this.allCity = [];
        }
      });
    },

    // 搜索
    search() {
      this.getAllCity(this.cityName);
    },

    // 跳转详情
    godetail(id) {
      this.$router.push({
        path: "/cityDetail",
        query: {
          id: id
        }
      });
    }
  }
};
</script>

<style scoped lang="less">
.cityContent {
  margin-top: 30px;
  width: 100%;
  height: 700px;
  overflow: auto;
  border: 1px solid black;
  ul {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    padding: 30px;
    li {
      width: 100px;
      height: 100px;
      line-height: 100px;
      text-align: center;
      border: 1px solid black;
      margin: 20px;
      cursor: pointer;
    }
  }
}
</style>
