<template>
  <div class="customer-list">
    <div style="margin-bottom: 16px">
      <a-button type="primary">
        <router-link to="addNewCustomer">添加新客户</router-link>
      </a-button>
      <a-popconfirm
        title="确定要删除此客户吗？"
        ok-text="Yes"
        cancel-text="No"
        v-on:confirm="confirmBulkDelete()"
      >
        <a-button
          type="primary"
          :disabled="!hasSelected"
          style="margin-left: 10px"
        >
          删除
        </a-button>
      </a-popconfirm>
      <span style="margin-left: 8px">
        <template v-if="hasSelected">
          {{ `Selected ${selectedRowKeys.length} items` }}
        </template>
      </span>
    </div>
    <a-table
      :row-selection="{
        selectedRowKeys: selectedRowKeys,
        onChange: onSelectChange,
      }"
      :columns="columns"
      :data-source="customers"
      :rowKey="(record) => record.id"
    >
      <span slot="action" slot-scope="text, record">
        <router-link :to="'/customer/' + record.id">编辑客户</router-link>
        <a-divider type="vertical" />
        <a-popconfirm
          title="确定要删除此客户吗？"
          ok-text="Yes"
          cancel-text="No"
          v-on:confirm="confirmDelete(record.id)"
        >
          <a href="#">删除</a>
        </a-popconfirm>
      </span>
      <span slot="decimalallowed" slot-scope="text">
        {{ text ? "是" : "否" }}
      </span>
    </a-table>
  </div>
</template>

<script>
// @ is an alias to /src
import { queryAllCustomer, checkCustomer, deleteCustomer } from "@/utils/db";
const columns = [
  {
    title: "ID",
    dataIndex: "id",
    key: "id",
    sorter: (a, b) => a.id - b.id,
    defaultSortOrder: "descend",
  },
  {
    dataIndex: "customerName",
    title: "客户名称",
    key: "name",
  },
  {
    title: "Action",
    key: "action",
    scopedSlots: { customRender: "action" },
  },
];
export default {
  name: "CustomerList",
  data() {
    return {
      customers: [],
      columns,
      selectedRowKeys: [],
    };
  },
  components: {},
  mounted() {
    this.getAllCustomers();
  },
  computed: {
    hasSelected() {
      return this.selectedRowKeys.length > 0;
    },
  },
  methods: {
    confirmDelete(customerId) {
    //   console.log(customerId);
      this.deleteCustomers([customerId]);
    },
    onSelectChange(selectedRowKeys) {
      // console.log("selectedRowKeys changed: ", selectedRowKeys);
      this.selectedRowKeys = selectedRowKeys;
    },
    getAllCustomers() {
      queryAllCustomer().then((data) => {
        this.customers = data;
      });
    },
    confirmBulkDelete() {
      if (this.selectedRowKeys.length > 0) {
        this.deleteCustomers(this.selectedRowKeys);
      } else {
        this.$message.warning("请先选择客户");
      }
    },
    deleteCustomers(ids) {
      checkCustomer(ids)
        .then(()=>{return deleteCustomer(ids)})
        .then(() => {
          this.$message.success("删除成功");
          this.getAllCustomers();
          this.selectedRowKeys = []
        })
        .catch((data) => {
          if (data.length > 0) {
            this.$message.error(
              '改客户已与订单绑定，无法删除',
              5
            );
          }
        });
    },
  },
};
</script>

<style>
</style>
