<template>
  <div>
    <i-menu mode="horizontal" theme="dark" active-name="1">
      <div class="layout-logo"></div>
      <div class="layout-nav">
        <menu-item name="1">
          <h1>
            <font color = #f0f8ff>
              公司考勤管理系统
            </font>
          </h1>
        </menu-item>
        <router-link to="/punch2">
          <menu-item name="2">
            <icon type="ios-contact"></icon>
            打卡
          </menu-item>
        </router-link>
        <router-link to="/check">
          <menu-item name="2">
            <icon type="ios-contact"></icon>
            考勤检查
          </menu-item>
        </router-link>
        <router-link to="/leavedeals">
          <menu-item name="3">
            <icon type="ios-contact"></icon>
            请假审批
          </menu-item>
        </router-link>
        <router-link to="/outdeals">
          <menu-item name="4">
            <icon type="ios-contact"></icon>
            外出审批
          </menu-item>
        </router-link>
        <router-link to="/leave2">
          <menu-item name="5">
            <icon type="ios-contact"></icon>
            请假申请
          </menu-item>
        </router-link>
        <router-link to="/out2">
          <menu-item name="6">
            <icon type="ios-contact"></icon>
            外出申请
          </menu-item>
        </router-link>
      </div>
    </i-menu>
    <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="80" style="position: absolute; top: 150px;width: 800px;left: 200px;">
      <FormItem label="卡号" prop="id">
        <Input v-model="formValidate.id" placeholder="输入您的卡号"></Input>
      </FormItem>
      <FormItem label="外出原因详情" prop="reason">
        <Input v-model="formValidate.reason" placeholder="详细说明您外出原因"></Input>
      </FormItem>
      <FormItem label="说明外出天数" prop="reason">
        <Input v-model="formValidate.days" placeholder="说明请假天数"></Input>
      </FormItem>
      <FormItem prop="date">
        <DatePicker type="daterange" @on-change="handleChange" format="yyyy-MM-dd" show-week-numbers placement="bottom-end" placeholder="选择外出时间"></DatePicker>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="handleSubmit('formValidate')">提交</Button>
        <Button @click="handleReset('formValidate')" style="margin-left: 8px">重置</Button>
      </FormItem>

    </Form>

  </div>
</template>

<script>
    export default {
        name: "out",
      data(){
        return{
          status:sessionStorage.getItem("userStatus"),
          formValidate: {
            id: sessionStorage.getItem("userId"),

            reason: '',
            days: 0,

          },
          dataValue: null,
          ruleValidate: {
            id: [
              { required: true, message: '卡号不能为空', trigger: 'blur' }
            ],

            reason: [
              { required: true, message: '需详细说明原因', trigger: 'change' }
            ],
            days: [
              { required: true, message: '天数不能为空', trigger: 'change' }
            ],

          }
        }
      },
      methods:{
        handleChange(daterange) {
          this.dataValue = daterange;
          //console.log(this.datavalue);
        },
        handleSubmit (name) {//部门经理请假
          this.$refs[name].validate((valid) => {
            if (valid) {
              this.$Message.success('Success!');
              console.log(this.formValidate);
              console.log(this.dataValue);
              console.log(this.dataValue[1]);
              console.log(this.dataValue[0]);//卡号，原因，原因类别，开始时间，结束时间，天数，
              this.$axios.post('/api/out', {status:this.status,id: this.formValidate.id, reason: this.formValidate.reason,  time_start: this.dataValue[0],time_end: this.dataValue[1],days:this.formValidate.days})
                .then((response) => {

                  if (response.data == true) {
                    this.$Message.info("提交成功");
                  }
                  else {
                    this.$Message.info("申请失败请重试");
                  }

                })
                .catch(function (error) {
                  //this.$Message.info("服务器出错，注册失败" + error.data);
                  console.log(error)
                });
            } else {
              this.$Message.error('Fail!');
            }
          })
        },
        handleReset (name) {
          this.$refs[name].resetFields();
        }

      }
    }
</script>

<style scoped>

</style>
