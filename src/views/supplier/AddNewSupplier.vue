<template>
  <div>
    <a-page-header
      style="border: 1px solid rgb(235, 237, 240); margin-bottom: 10px"
      title="添加新供应商"
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
        <a-button type="primary" html-type="submit"> 提交 </a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script>
import { insertSupplier } from "@/utils/db";
export default {
  name: "AddNewSupplier",
  data() {
    return {
      checkAllowDecimal: false,
      formLayout: "horizontal",
      form: this.$form.createForm(this, { name: "addNewSupplier" }),
    };
  },
  mounted() {},
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          // console.log("Received values of form: ", values);
          insertSupplier(values.supplierName, this.checkAllowDecimal).then(() => {
            // console.log("inset a supplier success!");
            this.$message.success("新供应商添加成功!");
          }).catch((err)=>{
              this.$message.error(err.message)
          });
        }
      });
    },
  },
};
</script>

