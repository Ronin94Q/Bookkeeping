<template>
  <div class="hero_list_detail">
    <div class="btn_wrap">
      <div class="left">
        <el-button type="info" plain @click="navBack">返回上一页</el-button>
      </div>
      <div class="right">
        <el-button type="danger" @click="clear_false_user">清空虚拟用户</el-button>
        <el-button type="primary" @click="import_false_user">导入虚拟用户</el-button>
        <el-button type="success" @click="userDialog = true">添加虚拟用户</el-button>
      </div>
    </div>
    <div class="tableBox">
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="rank" align="center" label="排名"></el-table-column>
        <el-table-column prop="userName" align="center" label="用户昵称"></el-table-column>
        <el-table-column prop="avatarUrl" align="center" label="用户头像">
          <template slot-scope="scope">
            <img
              :src="scope.row.avatarUrl"
              alt
              style="width:40px;height:40px;"
              v-if="scope.row.avatarUrl"
            />
          </template>
        </el-table-column>
        <el-table-column prop="profitAmount" align="center" label="总收益（元）"></el-table-column>
        <el-table-column prop="annualizedRate" align="center" label="总年化率（%）"></el-table-column>
        <el-table-column prop="type" align="center" label="用户类型">
          <template slot-scope="scope">
            <span v-if="scope.row.type == 1">虚拟用户</span>
            <span v-if="scope.row.type == 2" style="color:red;">真实用户</span>
          </template>
        </el-table-column>
        <el-table-column prop align="center" label="操作">
          <template slot-scope="scope">
            <el-button
              type="text"
              v-if="scope.row.type == 1"
              @click="set_increase_rules(scope.row.heroGrowthRules,scope.row.id)"
            >设置增长规则</el-button>
            <el-button type="text" v-if="scope.row.type == 1" @click="deleted(scope.row.id)">删除</el-button>
            <el-button
              type="text"
              v-if="scope.row.type == 2 && scope.row.heroStatus == 1"
              @click="hideOrShow(scope.row.id)"
            >隐藏</el-button>
            <el-button
              type="text"
              v-if="scope.row.type == 2 && scope.row.heroStatus == 2"
              @click="hideOrShow(scope.row.id)"
            >取消隐藏</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pageNumBox">
      <span>总计：{{totalSize}}条</span>
      <el-pagination
        background
        layout="prev, pager, next"
        :total="totalSize"
        :page-size="15"
        :current-page="currentPage"
        @current-change="pageChange($event)"
      ></el-pagination>
    </div>
    <!-- 新增虚拟用户弹窗 -->
    <el-dialog
      title="新增虚拟用户"
      :visible.sync="userDialog"
      width="400px"
      center
      :close-on-click-modal="false"
    >
      <el-form :model="userForm">
        <el-form-item label="用户名：">
          <el-input v-model="userForm.userName" style="width:200px;"></el-input>
        </el-form-item>
        <el-form-item label="总收益：">
          <el-input v-model="userForm.profitAmount" style="width:200px;"></el-input>
        </el-form-item>
        <el-form-item label="总年化：">
          <el-input v-model="userForm.annualizedRate" style="width:200px;"></el-input>
        </el-form-item>
        <el-form-item label="头像：">
          <el-upload
            class="avatar-uploader"
            action="/opadmin/fileStore/uploadFile"
            :show-file-list="false"
            :headers="myHeaders"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
            :on-error="uploadImgError"
          >
            <img v-if="imageUrl" :src="imageUrl" class="avatar" />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="userDialog = false">取 消</el-button>
        <el-button type="primary" @click="addHeroList">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 设置增长规则弹窗 -->
    <el-dialog
      title="设置增长随机数"
      :visible.sync="heroGrowthRulesDialog"
      width="500px"
      center
      :close-on-click-modal="false"
    >
      <el-form :model="heroGrowthRules">
        <el-form-item label="总收益：">
          <el-input v-model="heroGrowthRules.minProfitAmount" style="width:140px;"></el-input>
          <span>~</span>
          <el-input v-model="heroGrowthRules.maxProfitAmount" style="width:140px;"></el-input>
        </el-form-item>
        <el-form-item label="总年化：">
          <el-input v-model="heroGrowthRules.minAnnualizedRate" style="width:140px;"></el-input>
          <span>~</span>
          <el-input v-model="heroGrowthRules.maxAnnualizedRate" style="width:140px;"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="heroGrowthRulesDialog = false">取 消</el-button>
        <el-button type="primary" @click="addHeroGrowthRules">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 导入商家弹窗 -->
    <importExcel ref="importExcel"></importExcel>
  </div>
</template>
<script>
import {
  queryHeroList,
  addHeroList,
  deleteHeroById,
  deleteAllHero,
  addHeroGrowthRules,
  hideOrShowHeroList
} from "@/network/api";
import { getCookie } from "@/common/cookie.js";
import importExcel from "./ImportExcel";
export default {
  data() {
    return {
      fullscreenLoading: false,
      totalSize: 0,
      currentPage: 1,
      tableData: [],
      userForm: {
        userName: "",
        profitAmount: "",
        annualizedRate: "",
        icon: ""
      },
      myHeaders: { token: "" },
      imageUrl: "", // 上传的头像
      userDialog: false,
      heroGrowthRules: {
        id: "",
        heroId: "",
        minProfitAmount: "",
        maxProfitAmount: "",
        minAnnualizedRate: "",
        maxAnnualizedRate: ""
      },
      heroGrowthRulesDialog: false
    };
  },
  components: {
    importExcel
  },
  created() {
    this.myHeaders.token = getCookie().opadminToken;
    this.queryHeroList(1);
  },
  methods: {
    // 查询英雄榜列表
    queryHeroList(val) {
      let parms = {
        pageNumber: val ? val : this.currentPage,
        pageSize: 15
      };
      queryHeroList({ params: parms }).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          let content = res.data.content;
          this.totalSize = content.totalSize;
          this.tableData = content.items;
          this.currentPage = val ? val : this.currentPage;
        } else {
          this.$message.error(res.data.message);
        }
      });
    },
    // 返回上一页
    navBack() {
      this.$router.go(-1);
    },
    // 清空虚拟用户
    clear_false_user() {
      this.$confirm("", "确认清空虚拟用户?", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        center: true,
        showClose: false,
        closeOnClickModal: false
      })
        .then(() => {
          deleteAllHero().then(res => {
            if (res.data.messageCode == "MSG_1001") {
              this.queryHeroList(1);
              this.$message.success("操作成功");
            } else {
              this.$message.error(res.data.message);
            }
          });
        })
        .catch(() => {});
    },
    // 导入虚拟用户
    import_false_user() {
      this.$refs.importExcel.open();
    },
    // 上传图片之前
    beforeAvatarUpload(file) {
      const isLt1M = file.size / 1024 / 1024 < 1;
      this.fullscreenLoading = this.$loading({
        lock: true,
        text: "上传中",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)"
      });
      if (!isLt1M) {
        this.$message.error("上传头像图片大小不能超过 1MB!");
        this.fullscreenLoading.close();
        return false;
      }
    },
    // 上传图片成功
    handleAvatarSuccess(response, file, fileList) {
      this.imageUrl = response.content.url;
      this.userForm.icon = response.content.key;
      this.fullscreenLoading.close();
    },
    // 上传图片失败
    uploadImgError(err, file, fileList) {
      this.fullscreenLoading.close();
      this.$message.error("上传图片失败");
    },
    // 新增虚拟用户
    addHeroList() {
      let form = this.userForm;
      let isComplete = true;
      let reg = /(^-?(?:\d+|\d{1,3}(?:,\d{3})+)(?:\.\d{1,2})?$)/;
      for (let key in form) {
        if (!form[key]) {
          isComplete = false;
          break;
        }
      }
      if (!isComplete) {
        this.$message.error("请将表单填写完整");
        return;
      }
      if (!reg.test(form.profitAmount) || !reg.test(form.annualizedRate)) {
        this.$message.error("输入格式不正确");
        return;
      }
      addHeroList(this.qs.stringify(form)).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          this.userDialog = false;
          this.queryHeroList(1);
          this.$message.success("添加成功");
          for (let key in this.userForm) {
            this.userForm[key] = "";
          }
          this.imageUrl = "";
        } else {
          this.$message.error(res.data.message);
        }
      });
    },

    // 打开设置增长规则弹窗
    set_increase_rules(heroGrowthRules, id) {
      if (heroGrowthRules) {
        this.heroGrowthRules = JSON.parse(JSON.stringify(heroGrowthRules));
      } else {
        for (let key in this.heroGrowthRules) {
          this.heroGrowthRules[key] = "";
        }
        this.heroGrowthRules.heroId = id;
      }
      this.heroGrowthRulesDialog = true;
    },
    // 设置增长规则
    addHeroGrowthRules() {
      let form = this.heroGrowthRules;
      let reg = /(^-?(?:\d+|\d{1,3}(?:,\d{3})+)(?:\.\d{1,2})?$)/;
      if (
        !reg.test(form.minProfitAmount) ||
        !reg.test(form.maxProfitAmount) ||
        !reg.test(form.minAnnualizedRate) ||
        !reg.test(form.maxAnnualizedRate) ||
        form.minProfitAmount < -10000 ||
        form.maxProfitAmount > 10000 ||
        form.minProfitAmount > form.maxProfitAmount ||
        form.minAnnualizedRate < -100 ||
        form.maxAnnualizedRate > 100 ||
        form.minAnnualizedRate > form.maxAnnualizedRate
      ) {
        this.$message.error("输入格式不正确");
        return;
      }
      addHeroGrowthRules(form).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          this.heroGrowthRulesDialog = false;
          this.queryHeroList();
          this.$message.success("操作成功");
        } else {
          this.$message.error(res.data.message);
        }
      });
    },
    // 删除
    deleted(id) {
      this.$confirm("", "确认删除？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        center: true,
        showClose: false,
        closeOnClickModal: false
      })
        .then(() => {
          let parms = {
            id: id
          };
          deleteHeroById({ params: parms }).then(res => {
            if (res.data.messageCode == "MSG_1001") {
              this.queryHeroList();
              this.$message.success("操作成功");
            } else {
              this.$message.error(res.data.message);
            }
          });
        })
        .catch(() => {});
    },
    // 显示或者隐藏
    hideOrShow(id) {
      let parms = {
        id: id
      };
      hideOrShowHeroList({ params: parms }).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          this.queryHeroList();
          this.$message.success("操作成功");
        } else {
          this.$message.error(res.data.message);
        }
      });
    },
    // 翻页
    pageChange(val) {
      this.currentPage = val;
      this.queryHeroList();
    }
  }
};
</script>
<style lang="less" scoped>
.hero_list_detail {
  padding: 20px;
  box-sizing: border-box;
  .btn_wrap {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .tableBox {
    width: 100%;
    margin-top: 40px;
  }
  .pageNumBox {
    width: 100%;
    margin-top: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
}
</style>