<template>
  <div>
    <el-dialog title="选择商品" :visible.sync="DialogVisible" width="880px" center>
      <div>
        <div class="top">
          <span>商品分类</span>
          <el-cascader
            v-model="selectedOptions_class"
            @change="handleChangeClass"
            :options="options_class"
            :props="defaultParams"
          ></el-cascader>
          <el-input
            style="width:250px;margin:0 15px"
            v-model="goodsName"
            clearable
            placeholder="请输入商品名称或id"
          ></el-input>
          <el-button type="primary" @click="search(1)">搜 索</el-button>
          <el-button type="danger" @click="submit">保 存</el-button>
        </div>
        <el-checkbox-group
          v-model="checkedData"
          @change="handleCheckedCitiesChange"
          v-if="tableData.length>0"
          class="checkboxGroup"
        >
          <el-checkbox
            v-for="(item,index) in tableData"
            :label="item.id"
            :key="index"
            class="checkboxItem"
          >
            <div class="content">
              <div>
                <img :src="item.goodsImage" width="80" height="80" />
              </div>
              <div class="center">
                <div class="name">{{item.name}}</div>
                <div class="orgPrice">￥{{item.orgPrice}}</div>
              </div>
            </div>
          </el-checkbox>
        </el-checkbox-group>

        <div class="none" v-else>暂无这个商品</div>
        <el-pagination
          background
          layout="total, prev, pager, next, jumper"
          :total="Number(pageTotal)"
          :page-size="9"
          :current-page="currentPage"
          @current-change="pageChange($event)"
        ></el-pagination>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import {
  queryPlatAndMerchantGoodsList,
  getGoodsClassRequest
} from "@/network/api";
export default {
  props: {},
  data() {
    return {
      parentIndex: "",
      DialogVisible: false,
      tableData: [],
      currentPage: 1,
      pageTotal: 0,
      cur: "",
      defaultParams: {
        label: "name",
        value: "id",
        children: "nextLevelData",
        checkStrictly: true
      },
      options_class: [
        {
          id: "",
          name: "全部"
        }
      ],
      selectedOptions_class: [],
      goodsName: "",
      checkedData: [], //选中的数据
      goodsArr: "" // 已经添加的商品列表
    };
  },
  components: {},
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.search(1);
    this.getGoodsCategory();
  },
  methods: {
    open() {
      this.DialogVisible = true;
    },
    // 获取商品分类
    getGoodsCategory() {
      getGoodsClassRequest().then(res => {
        this.options_class = this.options_class.concat(
          this.getTreeData(res.data.content)
        );
      });
    },

    // 递归方法
    getTreeData(data) {
      // 循环遍历json数据
      for (var i = 0; i < data.length; i++) {
        if (data[i].nextLevelData.length < 1) {
          // children若为空数组，则将children设为undefined
          data[i].nextLevelData = undefined;
        } else {
          // children若不为空数组，则继续 递归调用 本方法
          this.getTreeData(data[i].nextLevelData);
        }
      }
      return data;
    },
    // 切换分类
    handleChangeClass(value) {},
    // 获取商品列表
    getplatGoodsList(param) {
      queryPlatAndMerchantGoodsList({ params: param }).then(res => {
        if (res.data.content) {
          this.tableData = res.data.content.items;
          this.pageTotal = res.data.content.totalSize;
        } else {
          this.tableData = [];
          this.pageTotal = 0;
        }
      });
    },
    // checkbox选中数据
    handleCheckedCitiesChange(value) {
      // console.log(value)
      this.checkedData = value;
    },
    // 点击保存事件
    submit() {
      if (this.checkedData.length <= 0) {
        this.$message.error("请选择商品");
        return;
      }
      this.DialogVisible = false;
      this.$emit("getgoods", this.checkedData);
      this.checkedData = [];
    },
    // 翻页
    pageChange(val) {
      this.search(val);
      this.currentPage = val;
      this.cur = "";
    },
    // 搜索
    search(val) {
      let obj = {};
      obj.pageNumber = val;
      obj.pageSize = 9;
      obj.goodsName = this.goodsName;
      obj.categoryId =
        this.selectedOptions_class == ""
          ? ""
          : this.selectedOptions_class[this.selectedOptions_class.length - 1];
      obj.issueStatus = 1;
      this.getplatGoodsList(obj);
      this.currentPage = 1;
    }
  }
};
</script>

<style scoped lang="less">
.top {
  margin-bottom: 20px;
}
.none {
  text-align: center;
  color: #cccccc;
  font-size: 20px;
  height: 200px;
  line-height: 200px;
}
.checkboxGroup {
  display: flex;
  flex-wrap: wrap;
  .checkboxItem {
    width: 260px;
    border: 1px solid black;
    padding: 15px;
    margin-right: 15px;
    margin-bottom: 15px;
    display: flex;
    justify-content: space-between;
    flex-direction: row-reverse;
    .content {
      display: flex;
      .center {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        margin: 0 15px;
        .name {
          width: 80px;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
        }
        .orgPrice {
          color: red;
          font-size: 16px;
        }
      }
    }
  }
}
</style>
