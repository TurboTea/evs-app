<template>
  <div>
    <a-page-header
      style="border: 1px solid rgb(235, 237, 240); margin-bottom: 10px"
      :title="headerTitle"
      sub-title=""
      @back="() => $router.go(-1)"
    ></a-page-header>
    <a-form
      :form="form"
      :label-col="{ span: 5 }"
      :wrapper-col="{ span: 12 }"
      @submit="handleSubmit"
    >
      <a-form-item label="供应商名称">
        <a-input
          v-decorator="[
            'supplierName',
            { rules: [{ required: true, message: '必须填写供应商名称' }] },
          ]"
        />
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
        <a-button type="primary" html-type="submit"> Submit </a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script>
import { getSupplier, updateSupplier } from "@/utils/db";
export default {
  name: "AddNewSupplier",
  data() {
    return {
      formLayout: "horizontal",
      form: this.$form.createForm(this, { name: "addNewSupplier" }),
      headerTitle: "编辑供应商",
      supplier: {},
    };
  },
  mounted() {
    if (this.$route.params.id) {
      getSupplier(this.$route.params.id).then((data) => {
        if (data) {
          this.supplier = data;
          this.headerTitle = "编辑供应商" + data.supplierName;
          this.form.setFieldsValue({
            supplierName: data.supplierName,
          });
        } else {
          console.log(data);
        }
      });
    }
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          // console.log("Received values of form: ", values);
          updateSupplier(this.supplier.id, values.supplierName)
            .then(() => {
              // console.log("inset a supplier success!");
              this.$message.success(this.supplier.supplierName + "修改成功!");
            })
            .catch((err) => {
              this.$message.error(err.message);
            });
        }
      });
    },
  },
};
</script>

