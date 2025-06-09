<script lang="ts" setup>
import type { VbenFormSchema } from '@vben/common-ui';
import type { Recordable } from '@vben/types';

import { computed, ref } from 'vue';
import { Page } from '@vben/common-ui';
import { $t } from '@vben/locales';
import { ElCard, ElDescriptions, ElDescriptionsItem, ElTag, ElButton, ElSteps, ElStep, ElTable, ElTableColumn, ElDivider } from 'element-plus';

defineOptions({ name: 'OrderDetail' });

// 订单ID，实际应用中可能从路由参数获取
const orderId = ref('ORD20250515001');

// 订单状态
const orderStatus = ref('已支付');

// 订单基本信息
const orderInfo = ref({
  orderNumber: 'ORD20250515001',
  orderTime: '2025/12/12 12:00',
  orderStatus: '已支付',
  orderAmount: 20000.00,
  productName: '新小镇 (USD/CNY)',
  productAddress: '某某省 某某市 某某区 某某路123号 某个园区 501室',
  contactPhone: '15932274453',
});

// 商品列表
const productList = ref([
  {
    name: '标准木质托盘',
    sku: 'WPT1210-S',
    price: 100.00,
    quantity: 100,
    subtotal: 10000.00
  },
  {
    name: '标准木质托盘',
    sku: 'WPT1210-S',
    price: 100.00,
    quantity: 100,
    subtotal: 10000.00
  }
]);

// 订单总计
const orderTotal = computed(() => {
  return {
    productTotal: productList.value.reduce((sum, item) => sum + item.subtotal, 0),
    shipping: 0.00,
    total: productList.value.reduce((sum, item) => sum + item.subtotal, 0)
  };
});

// 物流信息
const logisticsInfo = ref({
  deliveryMethod: '暂无',
  deliveryCompany: '暂无',
  trackingNumber: '暂无',
  deliveryTime: '未指定',
  receivedDate: '暂无'
});

// 支付信息
const paymentInfo = ref({
  paymentMethod: '线上支付转账',
  paymentStatus: '已支付',
  paymentAmount: 200.00
});

// 发票信息
const invoiceInfo = ref({
  invoiceType: '增值税普通发票',
  invoiceTitle: '公司名称',
  taxNumber: '税号信息',
  invoiceContent: '商品明细'
});

// 订单进度
const orderProgress = ref([
  {
    title: '当前商品状态',
    status: '等待卖方发货',
    time: '2025-06-03 09:20'
  },
  {
    title: '下单成功',
    status: '下单成功',
    time: '2025-06-03 09:20'
  }
]);

// 打印订单
const printOrder = () => {
  window.print();
};

// 返回订单列表
const goBack = () => {
  history.back();
};
</script>

<template>
  <Page>
    <div class="order-detail">
      <!-- 顶部操作栏 -->
      <div class="flex justify-between items-center mb-4">
        <div class="text-lg font-bold">订单详情: {{ orderId }}</div>
        <div class="flex gap-2">
          <ElButton @click="goBack">返回订单列表</ElButton>
          <ElButton type="primary" @click="printOrder">打印订单</ElButton>
        </div>
      </div>

      <!-- 订单基本信息 -->
      <ElCard class="mb-4">
        <template #header>
          <div class="flex justify-between items-center">
            <span>订单信息</span>
            <ElTag type="success" v-if="orderStatus === '已支付'">{{ orderStatus }}</ElTag>
            <ElTag type="warning" v-else-if="orderStatus === '待支付'">{{ orderStatus }}</ElTag>
            <ElTag type="info" v-else>{{ orderStatus }}</ElTag>
          </div>
        </template>
        <ElDescriptions :column="2" border>
          <ElDescriptionsItem label="订单号">{{ orderInfo.orderNumber }}</ElDescriptionsItem>
          <ElDescriptionsItem label="下单时间">{{ orderInfo.orderTime }}</ElDescriptionsItem>
          <ElDescriptionsItem label="订单状态">{{ orderInfo.orderStatus }}</ElDescriptionsItem>
          <ElDescriptionsItem label="订单金额">¥ {{ orderInfo.orderAmount.toFixed(2) }}</ElDescriptionsItem>
          <ElDescriptionsItem label="商品名称">{{ orderInfo.productName }}</ElDescriptionsItem>
          <ElDescriptionsItem label="联系电话">{{ orderInfo.contactPhone }}</ElDescriptionsItem>
          <ElDescriptionsItem label="收货地址" :span="2">{{ orderInfo.productAddress }}</ElDescriptionsItem>
        </ElDescriptions>
      </ElCard>

      <!-- 商品列表 -->
      <ElCard class="mb-4">
        <template #header>
          <span>商品列表</span>
        </template>
        <ElTable :data="productList" border style="width: 100%">
          <ElTableColumn prop="name" label="商品信息" />
          <ElTableColumn prop="sku" label="SKU" width="180" />
          <ElTableColumn prop="price" label="单价" width="120">
            <template #default="scope">
              ¥ {{ scope.row.price.toFixed(2) }}
            </template>
          </ElTableColumn>
          <ElTableColumn prop="quantity" label="数量" width="120" />
          <ElTableColumn prop="subtotal" label="小计" width="150">
            <template #default="scope">
              ¥ {{ scope.row.subtotal.toFixed(2) }}
            </template>
          </ElTableColumn>
        </ElTable>
        <div class="flex justify-end mt-4">
          <div class="w-64">
            <div class="flex justify-between py-2">
              <span>商品总额：</span>
              <span>¥ {{ orderTotal.productTotal.toFixed(2) }}</span>
            </div>
            <div class="flex justify-between py-2">
              <span>运费：</span>
              <span>¥ {{ orderTotal.shipping.toFixed(2) }}</span>
            </div>
            <ElDivider />
            <div class="flex justify-between py-2 text-lg font-bold">
              <span>订单总计：</span>
              <span class="text-red-500">¥ {{ orderTotal.total.toFixed(2) }}</span>
            </div>
          </div>
        </div>
      </ElCard>

      <!-- 物流信息和收货状态 -->
      <ElCard class="mb-4">
        <template #header>
          <span>物流信息与收货状态</span>
        </template>
        <ElDescriptions :column="2" border>
          <ElDescriptionsItem label="配送方式">{{ logisticsInfo.deliveryMethod }}</ElDescriptionsItem>
          <ElDescriptionsItem label="物流/生产公司">{{ logisticsInfo.deliveryCompany }}</ElDescriptionsItem>
          <ElDescriptionsItem label="运单号">{{ logisticsInfo.trackingNumber }}</ElDescriptionsItem>
          <ElDescriptionsItem label="发货日期">{{ logisticsInfo.deliveryTime }}</ElDescriptionsItem>
          <ElDescriptionsItem label="收货日期">{{ logisticsInfo.receivedDate }}</ElDescriptionsItem>
        </ElDescriptions>
      </ElCard>

      <!-- 财务收款与操作 -->
      <ElCard class="mb-4">
        <template #header>
          <span>财务收款与操作</span>
        </template>
        <ElDescriptions :column="2" border>
          <ElDescriptionsItem label="支付方式">{{ paymentInfo.paymentMethod }}</ElDescriptionsItem>
          <ElDescriptionsItem label="支付状态">
            <ElTag type="success" v-if="paymentInfo.paymentStatus === '已支付'">{{ paymentInfo.paymentStatus }}</ElTag>
            <ElTag type="warning" v-else>{{ paymentInfo.paymentStatus }}</ElTag>
          </ElDescriptionsItem>
          <ElDescriptionsItem label="线上支付金额">¥ {{ paymentInfo.paymentAmount.toFixed(2) }}</ElDescriptionsItem>
        </ElDescriptions>
      </ElCard>

      <!-- 订单进度 -->
      <ElCard class="mb-4">
        <template #header>
          <span>订单进度</span>
        </template>
        <ElSteps direction="vertical" :active="1">
          <ElStep v-for="(step, index) in orderProgress" :key="index" :title="step.title" :description="step.time">
            <template #description>
              <div>{{ step.status }}</div>
              <div>{{ step.time }}</div>
            </template>
          </ElStep>
        </ElSteps>
      </ElCard>
    </div>
  </Page>
</template>

<style scoped>
.order-detail {
  padding: 16px;
}
</style>
