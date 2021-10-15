<template>
	<div class="add_user_wrap">
    <el-button type="primary" size="small"  @click="pop_show = true">Add User</el-button>
      <el-dialog title="Add User" :visible.sync="pop_show" append-to-body>
        <el-form :model="userForm" status-icon :rules="rulesUserForm" ref="userForm" label-width="100px">
          <el-form-item label="Username:" prop="username">
            <el-input type="text" v-model="userForm.username" autocomplete="off" size="small" class="input_width"></el-input>
          </el-form-item>
          <el-form-item label="Key:" prop="password">
            <el-input type="password" v-model="userForm.password" autocomplete="off" size="small" class="input_width"></el-input>
          </el-form-item>
          <el-form-item label="Confirm Password" prop="passcheck">
            <el-input type="password" v-model="userForm.passcheck" autocomplete="off" size="small" class="input_width"></el-input>
          </el-form-item>
          <el-form-item label="Phone Number:" prop="mobile">
            <el-input type="text" v-model="userForm.mobile" autocomplete="off" size="small" class="input_width"></el-input>
          </el-form-item>
          <el-form-item label="Email:" prop="email">
            <el-input type="text" v-model="userForm.email" autocomplete="off" size="small" class="input_width"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('userForm')">Upload</el-button>
            <el-button @click="resetForm('userForm')">Reset</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
	</div>  
</template>

<script>
import cons from '@/components/constant';
export default {
  name: 'AddUser',
  data () {
    var validateUserName = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Username should not be null'));
        } else {
          var reUser = /^\w{5,20}$/;
          if(reUser.test(value))
          {
            callback()
          }else{
             callback(new Error('5 to 20 '));
          }
        }
    }; 
    var validatePassWord = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Key could not be Null'));
        } else {
          var rePass = /^[\w!@#$%^&*]{8,20}$/;
          if(rePass.test(value))
          {
            callback()
          }else{
             callback(new Error('8 to 20 with !@#$%^&*符号'));
          }
        }
    }; 
    var validatePassCheck = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('enter password again'));
        } else if (value !== this.userForm.password) {
          callback(new Error('you entered a different password!'));
        } else {
          callback();
        }
    };
    var validateMobile = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Phone number could not be null'));
        } else {
          var rePhone = /^1[34578]\d{9}$/;
          if(rePhone.test(value))
          {
            callback()
          }else{
             callback(new Error('phone number format incorrect'));
          }
        }
    };    
    return {
      pop_show:false,
      userForm:{
        username:'',
        password:'',
        passcheck:'',
        mobile:'',
        email:''
      },
      rulesUserForm:{
        username: [
            { validator: validateUserName, trigger: 'blur' }
        ],
        password: [
            { validator: validatePassWord, trigger: 'blur' }
        ],
        passcheck: [
            { validator: validatePassCheck, trigger: 'blur' }
        ],
        mobile: [
            { validator: validateMobile, trigger: 'blur' }
        ]
      }
    }
  },
  methods:{  
    submitForm(formName){
      this.$refs[formName].validate((valid) => {
        if (valid) {
        let token = localStorage.token;
        this.axios.post(cons.apis + '/users/', {
              "username": this.userForm.username,
              "mobile": this.userForm.mobile,
              "password": this.userForm.password,
              "email": this.userForm.email
            }, {
            headers: {
              'Authorization': 'JWT ' + token
            },
            responseType: 'json'           
        })
        .then(dat=>{
            if(dat.status==201){
              this.$message({
                type: 'success',
                message: 'add user success'
              });
              this.$emit('fnSetKey',this.userForm.username);             
              this.pop_show = false;
              this.resetForm('userForm');                     
            }
        }).catch(err=>{
           if(err.response.status==400){
              var errmsg = err.response.data;
              if(errmsg.username){
                this.$message({
                  type: 'info',
                  message: errmsg.username[0]
                });
              }
              if(errmsg.password){
                this.$message({
                  type: 'info',
                  message:errmsg.password[0]
                });
              }
              if(errmsg.mobile){
                this.$message({
                  type: 'info',
                  message:errmsg.mobile[0]
                });
              }
              if(errmsg.email){
                this.$message({
                  type: 'info',
                  message:errmsg.email[0]
                });
              }
           }
        });
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName){
      this.$refs[formName].resetFields();
    }
  }
}
</script>

<style scoped>
.input_width{
  width:450px;
}
</style>
