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
      <a-form-item label="客户名称">
        <a-input
          v-decorator="[
            'customerName',
            { rules: [{ required: true, message: '必须填写客户名称' }] },
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
import { getCustomer, updateCustomer } from "@/utils/db";
export default {
  name: "AddNewCustomer",
  data() {
    return {
      formLayout: "horizontal",
      form: this.$form.createForm(this, { name: "addNewCustomer" }),
      headerTitle: "编辑客户",
      customer: {},
    };
  },
  mounted() {
    if (this.$route.params.id) {
      getCustomer(this.$route.params.id).then((data) => {
        if (data) {
          this.customer = data;
          this.headerTitle = "编辑客户" + data.customerName;
          this.form.setFieldsValue({
            customerName: data.customerName,
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
          updateCustomer(this.customer.id, values.customerName)
            .then(() => {
              // console.log("inset a customer success!");
              this.$message.success(this.customer.customerName + "修改成功!");
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

