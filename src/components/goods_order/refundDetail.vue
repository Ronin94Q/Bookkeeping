<template>
  <div class="refundDetail">
    <!-- 订单信息 -->
    <div class="titleStyle">订单信息</div>
    <div class="orderInfoBox">
      <div class="orderInfoItem">订单编号：{{orderObj.orderNumber}}</div>
      <div class="orderInfoItem">订单金额：{{orderObj.orderAmount}}元</div>
      <div class="orderInfoItem">商品个数：{{orderObj.quantity}}个</div>
      <div class="orderInfoItem">
        下单时间：
        <span v-for="(item,index) in orderObj.orderTimeDetail" :key="index">
          <span v-if="item.status==1">{{item.statusTime | dateFormat}}</span>
        </span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.orderType != 28 && orderObj.orderType != 29">
        订单状态：
        <span v-if="orderObj.latestStatus==1">待支付</span>
        <span v-if="orderObj.latestStatus==2">待发货</span>
        <span v-if="orderObj.latestStatus==3">待使用</span>
        <span v-if="orderObj.latestStatus==4">待收货</span>
        <span v-if="orderObj.latestStatus==5">待评价</span>
        <span v-if="orderObj.latestStatus==6">已完成</span>
        <span v-if="orderObj.latestStatus==7">待发货-退款中</span>
        <span v-if="orderObj.latestStatus==8">待评价-退款中</span>
        <span v-if="orderObj.latestStatus==9">取消退款</span>
        <span v-if="orderObj.latestStatus==10">交易关闭</span>
        <span v-if="orderObj.latestStatus==11">退款失败</span>
        <span v-if="orderObj.latestStatus==12">已取消</span>
        <span v-if="orderObj.latestStatus==15">待成团</span>
        <span v-if="orderObj.latestStatus==16">拼团成功</span>
        <span v-if="orderObj.latestStatus==17">拼团失败</span>
        <span v-if="orderObj.latestStatus==18">拼团成功，已使用</span>
        <span v-if="orderObj.latestStatus==19">已过期</span>
      </div>
      <!-- 核销订单购买方式 -->
      <div class="orderInfoItem" v-if="orderObj.orderType == 28 || orderObj.orderType == 29">
        <span v-if="orderObj.buyMode==1">购买方式：普通拼团</span>
        <span v-if="orderObj.buyMode==2">购买方式：一折拼团</span>
      </div>
      <!-- 普通订单购买方式 -->
      <div class="orderInfoItem" v-if="orderObj.orderType != 28 && orderObj.orderType != 29">
        <span v-if="orderObj.orderType==1">购买方式：正常购买</span>
        <span v-if="orderObj.orderType==3">购买方式：新人免费体验订单</span>
        <span v-if="orderObj.orderType==4">购买方式：信用卡用户免费领订单</span>
        <span v-if="orderObj.orderType==5">购买方式：渠道合作活动订单</span>
        <span v-if="orderObj.orderType==6">购买方式：FreeBuy活动订单</span>
        <span v-if="orderObj.orderType==7">购买方式：FreeBuy订单</span>
        <span v-if="orderObj.orderType==8">购买方式：线下-普通订单</span>
        <span v-if="orderObj.orderType==9">购买方式：线下-FreeBuy订单</span>
        <span v-if="orderObj.orderType==10">购买方式：FreeBuy转正常购买</span>
        <span v-if="orderObj.orderType==11">购买方式：钻石合伙人订单</span>
        <span v-if="orderObj.orderType==12">购买方式：爱心捐助订单</span>
        <span v-if="orderObj.orderType==13">购买方式：好友赞助订单</span>
        <span v-if="orderObj.orderType==14">购买方式：FreeBuy赞助订单</span>
        <span v-if="orderObj.orderType == 15">购买方式：线上新人专区订单</span>
        <span v-if="orderObj.orderType == 16">购买方式：线下新人专区订单</span>
        <span v-if="orderObj.orderType == 17">购买方式：线上商品活动订单</span>
        <span v-if="orderObj.orderType == 18">购买方式：线下商品活动订单</span>
        <span v-if="orderObj.orderType == 19">购买方式：线上商品活动-FreeBuy订单</span>
        <span v-if="orderObj.orderType == 20">购买方式：线下商品活动-FreeBuy订单</span>
        <span v-if="orderObj.orderType == 21">购买方式：预售订单</span>
        <span v-if="orderObj.orderType == 22">购买方式：商品预售订单</span>
        <span v-if="orderObj.orderType == 23">购买方式：线下商家-普通购买订单</span>
        <span v-if="orderObj.orderType == 24">购买方式：线下商家-0成本购订单</span>
        <span v-if="orderObj.orderType == 26">购买方式：闪付订单</span>
        <span v-if="orderObj.orderType == 27">购买方式：一折购分期订单</span>
        <span v-if="orderObj.orderType == 28">购买方式：普通拼团订单</span>
        <span v-if="orderObj.orderType == 29">购买方式：一折购拼团订单</span>
        <span v-if="orderObj.orderType == 30">购买方式：自定义活动-{{orderObj.activityName}}</span>
      </div>
      <div class="orderInfoItem">备注：{{remark?remark:'无'}}</div>
      <div
        class="orderInfoItem"
        v-if="orderObj.useSeed == 1"
      >种子抵扣：{{orderObj.deductionAmount}}元（消费{{orderObj.deductionSeed}}种子）</div>
      <div class="orderInfoItem">
        订单优惠总金额：{{orderObj.totalDiscount}}元（
        <span
          v-if="orderObj.useSeed == 1"
        >积分减{{orderObj.deductionAmount}}元，</span>
        <span>钻石合伙人{{orderObj.discountRatio/10}}折减{{orderObj.discountAmount}}元</span>
        <span v-if="orderObj.useCoupon == 1">，钻石合伙人购物金减{{orderObj.shoppingAmount}}元</span>）
      </div>
      <div class="orderInfoItem" v-if="orderObj.whetherAdvanceSale == 1">
        <span>违约金：</span>
        <span v-if="orderObj.defaultAmountStatus == 1">待支付：</span>
        <span v-if="orderObj.defaultAmountStatus == 2">已支付：</span>
        <span v-if="orderObj.defaultAmountStatus == 3">已扣除：</span>
        <span v-if="orderObj.defaultAmountStatus == 4">已返还：</span>
        <span>{{orderObj.defaultAmount}}</span>
        <span>元</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.forumTopicResponse">
        <span>转让信息：</span>
        <span>售价：{{orderObj.forumTopicResponse.expectAmount}}元，</span>
        <span>共返：{{orderObj.forumTopicResponse.cashBackAmount}}元，</span>
        <span>剩余{{orderObj.forumTopicResponse.periodLeft}}期，</span>
        <span>截止{{orderObj.forumTopicResponse.maxReturnTime | dateFormat}}，</span>
        <span>每月{{orderObj.forumTopicResponse.perReturnDay}}号，</span>
        <span>每期返还{{orderObj.forumTopicResponse.perReturnAmount}}，</span>
        <span>年收益率{{orderObj.forumTopicResponse.annualizedRate}}，</span>
        <span>内容：{{orderObj.forumTopicResponse.content}}</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.orderType == 28 || orderObj.orderType == 29">
        <span>自提点：</span>
        <span>{{orderObj.orderAddressDetail.districtAddress}}</span>
        <span>{{orderObj.orderAddressDetail.detailedAddress}}</span>
      </div>
    </div>
    <!-- 商品信息 -->
    <div class="titleStyle">商品信息</div>
    <div class="orderInfoBox">
      <div class="orderInfoItem" v-for="(item,index) in orderObj.orderGoodsDetail" :key="index">
        商品名：{{item.goodsName}} ( 分类：{{item.goodsCategory}},规格：{{item.specDesc}},折扣：{{item.dctRate}}%,原价：{{item.orgPrice}}元,返现：{{item.cashBack?item.cashBack:0}}元
        <span
          v-if="item.period==0"
        >，返现：立返</span>
        <span v-else-if="item.period==null"></span>
        <span v-else>
          ，返现：{{item.period}}期 (
          <span v-if="item.period==item.returnedPeriod || item.period < 1">
            {{item.returnedPeriod}}期进行中,
            返现时间：
            <span
              v-for="(ite,inde) in item.orderGoodsCashBackItem"
              :key="inde"
            >
              <span v-if="ite.period==item.returnedPeriod || item.period < 1">{{ite.returnTime | dateFormat}}</span>
            </span>
          </span>
          <span v-else>
            {{item.returnedPeriod+1}}期进行中,
            返现时间：
            <span
              v-for="(ite,inde) in item.orderGoodsCashBackItem"
              :key="inde"
            >
              <span v-if="ite.period==item.returnedPeriod+1">{{ite.returnTime | dateFormat}}</span>
            </span>
          </span>
          ))
        </span>
        <span>购买数量：{{item.quantity}}个，</span>
        <span v-if="item.orderGoodsApplyRefund">退款状态：</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 1">退款中，</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 2">商家已同意-退款中，</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 3">已发货-退款中，</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 4">退款失败，</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 5">退款成功，</span>
        <span v-if="item.orderGoodsApplyRefund && item.orderGoodsApplyRefund.status == 6">取消退款，</span>
        <span>优惠金额：钻石合伙人{{item.discountRatio?item.discountRatio/10:0}}折减{{item.discountAmount?item.discountAmount:0}}元，钻石合伙人购物金减{{item.shoppingAmount?item.shoppingAmount:0}}元，积分减{{item.deductionAmount?item.deductionAmount:0}}元</span>
      </div>
    </div>
    <!-- 赠送内容信息 -->
    <div class="titleStyle" v-if="orderObj.activityHistory">赠送内容信息</div>
    <div class="orderInfoBox" v-if="orderObj.activityHistory">
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardSeed == 1">
        <span>送种子：{{orderObj.activityHistory.rewardSeedDto.param1}}颗</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardCashBack == 1">
        <span>送返现：返现金额{{orderObj.activityHistory.rewardCashBackDto.param1}}返现期数{{orderObj.activityHistory.rewardCashBackDto.param2}}</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardGoods == 1">
        <span>赠送商品：</span>
        <span
          v-for="(item,index) in orderObj.activityHistory.rewardGoodsDtoList"
          :key="index"
        >{{item.goodsName}}+{{item.specDesc}}&nbsp;&nbsp;&nbsp;</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardMember == 1">
        <span>送身份特权：{{orderObj.activityHistory.rewardMemberDto.param1 == '3'?'合伙人':'钻石合伙人'}}（{{orderObj.activityHistory.rewardMemberDto.param2}}个月）</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardPaymentAmount == 1">
        <span>减少支付金额：打{{orderObj.activityHistory.rewardPaymentAmountDto.param1}}折，减{{orderObj.activityHistory.rewardPaymentAmountDto.param2}}元</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.activityHistory.rewardExpressFee == 1">包邮</div>
    </div>
    <!-- 用户信息 -->
    <div class="titleStyle">用户信息</div>
    <div class="orderInfoBox">
      <div class="orderInfoItem">用户编号：{{orderObj.userId}}</div>
      <div class="orderInfoItem">用户名：{{orderObj.userName}}</div>
      <div class="orderInfoItem">联系方式：{{orderObj.mobileNumber}}</div>
      <div
        class="orderInfoItem"
        v-if="orderObj.orderAddressDetail && orderObj.orderType != 28 && orderObj.orderType != 29"
      >收货信息：收货人：{{orderObj.orderAddressDetail.receiverName}}，手机号码：{{orderObj.orderAddressDetail.mobileNumber}}，所在地区：{{orderObj.orderAddressDetail.districtAddress}}，详细地址：{{orderObj.orderAddressDetail.detailedAddress}}</div>
      <div class="orderInfoItem">上级：{{orderObj.parentName || '无'}}</div>
    </div>
    <div class="titleStyle" v-if="!orderObj.deliveryType">物流信息</div>
    <div class="titleStyle" v-else>配送信息</div>
    <div class="orderInfoBox" v-if="orderObj.deliveryType == 1">
      <span class="orderInfoItem">【到店自提】</span>
      <span class="orderInfoItem" v-if="orderObj.payType == 1">【在线支付】</span>
      <span class="orderInfoItem" v-if="orderObj.payType == 2">【到付】</span>
    </div>
    <div class="orderInfoBox" v-else-if="orderObj.deliveryType == 2">
      <span class="orderInfoItem">【商家配送】</span>
      <span class="orderInfoItem" v-if="orderObj.payType == 1">【在线支付】</span>
      <span class="orderInfoItem" v-if="orderObj.payType == 2">【到付】</span>
      <span class="orderInfoItem">【配送费：{{orderObj.expressFee}}】</span>
      <span class="orderInfoItem">【地址：{{orderObj.orderAddressDetail.detailedAddress}}】</span>
    </div>
    <div class="orderInfoBox" v-else-if="isDeliverGoods && !orderObj.deliveryType">
      <span class="orderInfoItem">此商品无需发货</span>
    </div>
    <div class="orderInfoBox" v-else>
      <div
        v-for="(item,index) in orderObj.orderLogisticsDetailList"
        :key="index"
        style="width:100%;margin-bottom:15px;"
      >
        <span class="orderInfoItem">{{item.goodsDetailSpecDesc}}</span>
        <span class="orderInfoItem">物流：{{item.companyName ? item.companyName : '暂无'}}</span>
        <span class="orderInfoItem">物流单号：{{item.trackingNumber ? item.trackingNumber : '暂无'}}</span>
      </div>

      <div v-for="(ite) in orderObj.orderGoodsTobeShippedList" :key="ite.id" style="width:100%">
        <span class="orderInfoItem">{{ite.goodsName}}-{{ite.specDesc}}</span>
        <span class="orderInfoItem">物流：暂无</span>
        <span class="orderInfoItem">物流单号：暂无</span>
      </div>

      <div class="orderInfoItem" v-if="orderObj.latestStatus==5">
        <span class="mark">已送达</span>
      </div>
    </div>

    <!-- 用户使用情况 -->
    <div class="titleStyle" v-if="orderObj.orderType == 28 || orderObj.orderType == 29">用户使用情况</div>
    <div class="orderInfoBox" v-if="orderObj.orderType == 28 || orderObj.orderType == 29">
      <div class="orderInfoItem" v-if="orderObj.latestStatus==1">待支付</div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==12">已取消</div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==15">待成团</div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==16">
        <span>已上传</span>
        <span v-if="orderObj.validStatus == 1">（过期待验证）</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==10">
        <span v-if="orderObj.verificationCode">已上传（已退款）</span>
        <span v-if="!orderObj.verificationCode">待成团（已退款）</span>
      </div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==17">拼团失败</div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==18">已上传（过期已用）</div>
      <div class="orderInfoItem" v-if="orderObj.latestStatus==19">已上传（过期未用）</div>
    </div>

    <div class="titleStyle">退款信息</div>
    <div class="refundOrderInfoItem">
      <div class="refundItems">
        <div class="title">| 退款商品</div>
        <div class="productInfo">
          <img :src="orderGoodsDetail.imageUrl" />
          <div class="right">
            <div class="info">
              <span>{{orderGoodsDetail.goodsName}}</span>
              <span>¥{{orderGoodsDetail.price}}</span>
            </div>
            <div class="info">
              <span>{{orderGoodsDetail.specDesc}}</span>
              <span>X {{orderGoodsDetail.quantity}}</span>
            </div>
          </div>
        </div>
        <div class="title">| 退款信息</div>
        <div class="items">
          <span>退款编号：</span>
          <span>{{orderGoodsApplyRefund.refundNumber}}</span>
        </div>
        <div class="items">
          <span>申请时间：</span>
          <span>{{orderGoodsApplyRefund.createTime | dateFormat}}</span>
        </div>
        <div class="items" v-if="orderObj.orderType != 28 && orderObj.orderType != 29">
          <span>货物状态：</span>
          <span v-if="orderGoodsApplyRefund.cargoStatus == 1">未收到货</span>
          <span v-if="orderGoodsApplyRefund.cargoStatus == 2">已收到货</span>
        </div>
        <div class="items">
          <span>退款金额：</span>
          <span>¥{{orderGoodsApplyRefund.refundAmount}}</span>
        </div>
        <div class="items">
          <span>退款理由：</span>
          <span>{{orderGoodsApplyRefund.desc}}</span>
        </div>
        <div class="items">
          <span>处理时间：</span>
          <span>{{orderGoodsApplyRefund.operateTime | dateFormat}}</span>
        </div>
      </div>
      <div class="refundItems">
        <div class="title">| 退款说明凭证</div>
        <div class="explainBox">
          <div class="explain">{{orderGoodsApplyRefund.remark?orderGoodsApplyRefund.remark:'无'}}</div>
          <div class="imgBox">
            <img
              v-for="(item,index) in orderGoodsApplyRefund.orderGoodsApplyRefundImageList"
              :key="index"
              :src="item.imageUrl"
            />
          </div>
        </div>
        <div class="title">| 退款物流</div>
        <div class="items">
          <span>快递名称：</span>
          <span v-if="orderGoodsApplyRefund.logisticsName">{{orderGoodsApplyRefund.logisticsName}}</span>
          <span v-else>无</span>
        </div>
        <div class="items">
          <span>快递单号：</span>
          <span v-if="orderGoodsApplyRefund.trackingNumber">{{orderGoodsApplyRefund.trackingNumber}}</span>
          <span v-else>无</span>
        </div>
        <el-button
          type="primary"
          style="margin-top:20px;"
          @click="getLogisticsInfo(orderGoodsApplyRefund.trackingNumber,orderGoodsApplyRefund.id)"
        >物流信息</el-button>
      </div>
    </div>
    <div class="btnWrap">
      <el-button
        type="success"
        v-if="orderGoodsApplyRefund.status == 1"
        @click="agree(orderGoodsApplyRefund.cargoStatus,orderGoodsApplyRefund.id,1)"
      >同意</el-button>
      <el-button
        type="danger"
        v-if="orderGoodsApplyRefund.status == 1 || orderGoodsApplyRefund.status == 2 || orderGoodsApplyRefund.status == 3"
        @click="refuse(orderGoodsApplyRefund.id,2)"
      >拒绝</el-button>
      <el-button
        type="primary"
        v-if="orderGoodsApplyRefund.status == 2 || orderGoodsApplyRefund.status == 3"
        @click="logistics(orderGoodsApplyRefund.status,orderGoodsApplyRefund.id,1)"
      >物流</el-button>
      <el-button
        type="primary"
        v-if="orderGoodsApplyRefund.status == 4"
        @click="refuseReason(orderGoodsApplyRefund.refusalCause)"
      >拒绝原因</el-button>
    </div>

    <!-- 退货物流信息弹窗 -->
    <el-dialog title="退货物流信息" :visible.sync="dialogVisible" width="60%">
      <div class="dialogTitle">
        <span>快递名称：{{logisticsInfo.company}}</span>
        <span>快递单号：{{logisticsInfo.no}}</span>
      </div>
      <div class="dialogItems" v-for="(item,index) in logisticsInfo.list" :key="index">
        <span>{{item.datetime}}</span>
        <span>{{item.remark}}</span>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="sign(1)" v-if="showBtnSure">确认签收并退款</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import { queryOrderRefund, queryLogistic, refundAudits } from "@/network/api";
export default {
  data() {
    return {
      id: "",
      orderId: "",
      orderObj: {},
      remark: "", // 备注
      orderGoodsDetail: "",
      orderGoodsApplyRefund: "",
      dialogVisible: false,
      logisticsInfo: {
        // 物流信息
        company: "",
        no: ""
      },
      showBtnSure: true,
      isDeliverGoods: false // 判断商品是否无需发货
    };
  },
  created() {
    this.id = this.$route.query.id;
    this.queryOrderRefund();
  },
  methods: {
    // 获取订单详情
    queryOrderRefund() {
      let params = {
        refundId: this.id
      };
      queryOrderRefund({ params: params }).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          if (res.data.content) {
            let data = res.data.content;
            this.orderObj = data.orderResponse;
            this.orderGoodsDetail = data.orderGoodsDetail;
            this.orderGoodsApplyRefund = this.orderGoodsDetail.orderGoodsApplyRefund;
            if (this.orderObj.orderAddressDetail) {
              this.remark = this.orderObj.orderAddressDetail.remark;
            }
            // 判断商品是否无需发货
            if (this.orderObj.orderTimeDetail) {
              this.orderObj.orderTimeDetail.forEach(val => {
                if (val.remark == "NO_LOGISTICS") {
                  this.isDeliverGoods = true;
                }
              });
            }
          }
        } else {
          this.$message.error(res.data.message);
        }
      });
    },
    // 查看物流信息
    getLogisticsInfo(trackingNumber, orderId) {
      let has = trackingNumber ? 1 : 2;
      this.orderId = orderId;
      this.showBtnSure = false;
      // has: 1-已填写物流信息，2-未填写物流信息
      if (has == 1) {
        let data = {
          refundId: orderId
        };
        queryLogistic({ params: data }).then(res => {
          if (res.data.messageCode == "MSG_1001") {
            this.dialogVisible = true;
            if (res.data.content) {
              this.logisticsInfo = res.data.content;
            }
          } else {
            this.$message.error(res.data.message);
          }
        });
      } else if (has == 2) {
        this.$message("用户暂未上传退货物流信息");
      }
    },
    // 退款审核
    refundAudit(orderId, type, remark) {
      let params = {
        orderId: orderId,
        type: type,
        remark: remark ? remark : ""
      };
      refundAudits(params).then(res => {
        if (res.data.messageCode == "MSG_1001") {
          this.$message.success("操作成功");
          this.queryOrderRefund();
        } else {
          this.$message.error(res.data.message);
        }
      });
    },
    // 同意
    agree(cargoStatus, orderId, type) {
      let tips;
      if (cargoStatus == 2) {
        tips = "是否同意当前商品退款？";
      } else if (cargoStatus == 1) {
        tips =
          "当前退款商品用户无需退货，同意退款后系统将退款至用户，是否同意当前商品退款？";
      }
      this.$confirm(tips, "同意退款", {
        confirmButtonText: "同意",
        cancelButtonText: "取消"
      })
        .then(() => {
          this.refundAudit(orderId, type);
        })
        .catch(() => {});
    },
    // 拒绝
    refuse(orderId, type) {
      this.$prompt("是否拒绝当前商品退款？", "拒绝退款", {
        confirmButtonText: "拒绝",
        cancelButtonText: "取消",
        inputPlaceholder: "请填写拒绝退款原因，（100字以内）",
        inputType: "textarea",
        inputPattern: /\S/,
        inputErrorMessage: "理由不能为空"
      })
        .then(({ value }) => {
          if (value.length > 100) {
            this.$message.error("拒绝退款原因字数不能大于100");
            return;
          }
          this.refundAudit(orderId, type, value);
        })
        .catch(() => {});
    },
    // 物流
    logistics(status, orderId, type) {
      this.orderId = orderId;
      this.showBtnSure = true;
      if (status == 3) {
        let data = {
          refundId: orderId
        };
        queryLogistic({ params: data }).then(res => {
          if (res.data.messageCode == "MSG_1001") {
            this.dialogVisible = true;
            if (res.data.content) {
              this.logisticsInfo = res.data.content;
            }
          } else {
            this.$message.error(res.data.message);
          }
        });
      } else {
        this.$confirm(
          "用户暂未上传退货物流信息，如果您已收到货物或其他情况无需退货且双方已经协商成功的请确认签收并退款。",
          "退货物流信息",
          {
            confirmButtonText: "确认签收并退款",
            cancelButtonText: "取消"
          }
        )
          .then(() => {
            this.refundAudit(orderId, type);
            this.dialogVisible = false;
          })
          .catch(() => {});
      }
    },
    // 已填写物流信息弹窗点击确认签收并退款
    sign() {
      this.refundAudit(this.orderId, 1);
      this.dialogVisible = false;
    },
    // 拒绝原因
    refuseReason(refusalCause) {
      refusalCause = refusalCause ? refusalCause : "无";
      this.$alert(refusalCause, "", {
        confirmButtonText: "确定",
        center: true,
        showClose: false,
        callback: action => {}
      });
    }
  }
};
</script>
<style lang="less" scoped>
.refundDetail {
  padding: 20px;
  box-sizing: border-box;
  .titleStyle {
    width: 100%;
    height: 30px;
    font-size: 14px;
    border-bottom: solid 1px #ccc;
  }
  .orderInfoBox {
    width: 100%;
    height: auto;
    border: solid 1px #ccc;
    padding: 30px 60px 30px 60px;
    box-sizing: border-box;
    margin-top: 20px;
    margin-bottom: 30px;
    display: flex;
    flex-wrap: wrap;
    .orderInfoItem {
      font-size: 14px;
      color: #333;
      line-height: 30px;
      margin-right: 20px;
      > .mark {
        color: antiquewhite;
        font-size: 20px;
        font-weight: bold;
        margin-left: 20px;
      }
    }
    .orderInfoItem-1 {
      width: 100%;
      font-size: 14px;
      color: #333;
      line-height: 30px;
      margin-right: 60px;
      > span {
        margin-right: 60px;
      }
    }
    .orderInfoItem-btn {
      margin-left: 20px;
    }
  }
  .refundOrderInfoItem {
    width: 100%;
    height: auto;
    border: solid 1px #ccc;
    padding: 10px;
    box-sizing: border-box;
    margin-top: 20px;
    margin-bottom: 30px;
    display: flex;
    justify-content: space-between;
    .refundItems {
      width: 45%;
      .title {
        font-weight: bold;
        padding: 20px 0px;
      }
      .productInfo {
        width: 100%;
        display: flex;
        justify-content: space-between;
        img {
          width: 100px;
          height: 100px;
        }
        .right {
          width: calc(100% - 120px);
          .info {
            padding: 12px 0px;
            display: flex;
            justify-content: space-between;
          }
        }
      }
      .explainBox {
        width: 100%;
        .imgBox {
          width: 100%;
          display: flex;
          flex-wrap: wrap;
          img {
            width: 100px;
            height: 100px;
            margin: 4px;
          }
        }
      }
      .items {
        width: 100%;
        margin-top: 10px;
        display: flex;
        justify-content: space-between;
      }
    }
  }
  .btnWrap {
    width: 100%;
    margin: 20px 0px;
    display: flex;
    justify-content: center;
  }
  .dialogTitle {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
  }
  .dialogItems {
    width: 100;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 10px;
    font-size: 14px;
    color: #999;
  }
}
</style>