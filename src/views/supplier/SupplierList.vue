<template>
  <div class="supplier-list">
    <div style="margin-bottom: 16px">
      <a-button type="primary">
        <router-link to="addNewSupplier">添加新供应商</router-link>
      </a-button>
      <a-popconfirm
        title="确定要删除此供应商吗？"
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
      :data-source="suppliers"
      :rowKey="(record) => record.id"
    >
      <span slot="action" slot-scope="text, record">
        <router-link :to="'/supplier/' + record.id">编辑供应商</router-link>
        <a-divider type="vertical" />
        <a-popconfirm
          title="确定要删除此供应商吗？"
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
import { queryAllSupplier, checkSupplier, deleteSupplier } from "@/utils/db";
const columns = [
  {
    title: "ID",
    dataIndex: "id",
    key: "id",
    sorter: (a, b) => a.id - b.id,
    defaultSortOrder: "descend",
  },
  {
    dataIndex: "supplierName",
    title: "供应商名称",
    key: "name",
  },
  {
    title: "Action",
    key: "action",
    scopedSlots: { customRender: "action" },
  },
];
export default {
  name: "SupplierList",
  data() {
    return {
      suppliers: [],
      columns,
      selectedRowKeys: [],
    };
  },
  components: {},
  mounted() {
    this.getAllSuppliers();
  },
  computed: {
    hasSelected() {
      return this.selectedRowKeys.length > 0;
    },
  },
  methods: {
    confirmDelete(supplierId) {
    //   console.log(supplierId);
      this.deleteSuppliers([supplierId]);
    },
    onSelectChange(selectedRowKeys) {
      // console.log("selectedRowKeys changed: ", selectedRowKeys);
      this.selectedRowKeys = selectedRowKeys;
    },
    getAllSuppliers() {
      queryAllSupplier().then((data) => {
        this.suppliers = data;
      });
    },
    confirmBulkDelete() {
      if (this.selectedRowKeys.length > 0) {
        this.deleteSuppliers(this.selectedRowKeys);
      } else {
        this.$message.warning("请先选择供应商");
      }
    },
    deleteSuppliers(ids) {
      checkSupplier(ids)
        .then(()=>{return deleteSupplier(ids)})
        .then(() => {
          this.$message.success("删除成功");
          this.getAllSuppliers();
          this.selectedRowKeys = []
        })
        .catch((data) => {
          if (data.length > 0) {
            this.$message.error(
              '改供应商已与订单绑定，无法删除',
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
