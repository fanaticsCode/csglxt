<template>
    <el-container id="frameBox">
    <!-- 左边的盒子 -->
    <LeftMenu></LeftMenu>
    

    <!-- 右边的盒子 包含 变化内容 头部 尾部-->
    <el-container id="mainContent">
      <!-- 右边上面的盒子 -->
      <RightTop></RightTop>


      <!-- 中间内容盒子 --------------------内容模块-->
      <el-main>
        <!-- 卡片组件 -->
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span class="spans">修改账号密码</span>
            <el-button style="float: right; padding: 3px 10px" type="text">添加账号</el-button>
            <el-button style="float: right; padding: 3px 10px" type="text">管理账号</el-button>
            <el-button style="float: right; padding: 3px 10px" type="text">修改密码</el-button>
          </div>
          <el-form
            :model="ruleForm2"
            status-icon
            :rules="rules2"
            ref="ruleForm2"
            label-width="100px"
            class="demo-ruleForm"
            label-position="top"
          >
            <el-form-item label="原密码" prop="userpwd">
              <el-input type="password" v-model="ruleForm2.userpwd" autocomplete="off" class="inputbox"></el-input>
            </el-form-item>
            <el-form-item label="新密码" prop="newpass">
              <el-input type="password" v-model="ruleForm2.newpass" autocomplete="off" class="inputbox"></el-input>
            </el-form-item>
            <el-form-item label="确认新密码" prop="checkPass">
              <el-input type="password" v-model="ruleForm2.checkPass" autocomplete="off" class="inputbox"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="submitForm('ruleForm2')">修改</el-button>
            </el-form-item>
          </el-form>
        </el-card>
        </el-main>


      
      <!-- 下面的盒子 -->
      <RightBottom></RightBottom>
    </el-container>
  </el-container>
</template>




<script>
  // 引入导航组件
  import LeftMenu from '../components/leftMenu';
  import RightTop from '../components/rightTop.vue';
  import RightBottom from '../components/rightBottom.vue';
export default {
  data(){
    // 验证密码是否一致
    var validatePassword = (rule, value, callback) => {
      if (value !== this.ruleForm2.newpass) {
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();
      }
    };


    return {
      ruleForm2: {
        userpwd: "",
        newpass: "",
        checkPass: "",
      },
      rules2: {
        userpwd: [
          // require:true 必填   blur失焦事件     出错信息
          { required: true, trigger: "blur", message: "原密码必须填写" },
          //min: 6 最小长度   max: 18 最大长度
          {
            min: 1,
            max: 18,
            message: "用户名长度在 6 到 18 个字符",
            trigger: "blur"
          }
        ],
        newpass: [
          { required: true, trigger: "blur", message: "新密码必须填写" },
          {
            min: 1,
            max: 12,
            message: "密码长度在 6 到 12 个字符",
            trigger: "blur"
          }
        ],
        checkPass: [
          { required: true, trigger: "blur", message: "新密码必须填写" },
          {
            min: 1,
            max: 12,
            message: "密码长度在 6 到 12 个字符",
            trigger: "blur"
          },
          { validator: validatePassword, trigger: 'blur' }
        ],
      }
    };
  },




    components: {
    //注册组件
    LeftMenu,
    RightTop,
    RightBottom
  },


    methods: {
    // 提交表单的方法
    /* submitForm(formName) {
      // 调用组件验证验证的方法
      this.$refs[formName].validate(valid => {
        console.log(validate,"www");
        //valid参数表示验证的结果，true表示验证通过，false验证失败
        if (valid) {
          alert("登录成功✔");
          //使用路由对象的push实现跳转(this指向实例)
          // this.$router.push('/');
        } else {
          // alert("登录失败✖");
          return false;
        }
      });
    }, */

    submitForm(formName) {
      // 调用组件验证验证的方法
      this.$refs[formName].validate(valid => {
        //valid参数表示验证的结果，true表示验证通过，false验证失败
        if (valid) {
          // console.log(this.ruleForm2);
          //通过前端验证 才发ajax去后端数据库改数据
          this.axios.post(this.apiHost+'/user/pwdEdit',this.qs.stringify(this.ruleForm2))
          .then((result)=>{
            console.log("后端返回的数据：",result.data);
            //返回的数据 做判断
            if(result.data.isOk){
                this.$message({
                message: result.data.msg,
                type: 'success'
              });
              setTimeout(()=>{
                this.$router.push('/login')
              },600)
            } else{
              this.$message.error(result.data.msg);
            }
          }).catch((err)=>{
            this.$message.error('错了哦'+result.data.msg);
          })
        } else {
          return false;
        }
      });
    }









  }
}
</script>





<style>
.el-form-item__label{height:30px;}
.el-form-item--feedback{height:90px;}
/* 清除文字的下内边距 */
.el-form-item--feedback .el-form-item__label{padding:0px;}
/* 确认修改按钮 */
.el-form-item__content{margin-top:15px;}


.el-input{width:250px;}
.el-input--suffix{width:140px;}





.el-form-item{margin-bottom:10px;}
.el-card__body{padding:0px 30px;}

</style>
