<template>
	<div class="add_admin_wrap">
     <el-button type="primary" size="small" @click="pop_show = true" class="pull-right">Add Admin</el-button>     
     <el-dialog title="Add Admin" :visible.sync="pop_show" append-to-body width="95%">
        <el-form :model="adminForm" :inline="true" status-icon :rules="rulesAdminForm" ref="adminForm" label-width="70px">
          <el-form-item label="Username:" prop="username">
            <el-input type="text" v-model="adminForm.username" autocomplete="off" size="small"></el-input>
          </el-form-item>          
          <el-form-item label="Key:" prop="password">
            <el-input type="text" v-model="adminForm.password" autocomplete="off" size="small"></el-input>
          </el-form-item>
          <el-form-item label="Phone Numer" prop="mobile">
            <el-input type="text" v-model="adminForm.mobile" autocomplete="off" size="small"></el-input>
          </el-form-item>
          <el-form-item label="EMail Address" prop="email">
            <el-input type="text" v-model="adminForm.email" autocomplete="off" size="small"></el-input>
          </el-form-item>


          <el-form-item>
            <el-button type="primary" @click="submitForm">Upload</el-button>
            <el-button @click="resetForm('adminForm')">Reset</el-button>
          </el-form-item>
        </el-form>
    </el-dialog>
	</div>  
</template>

<script>
import cons from '@/components/constant';
let token = localStorage.token;
export default {
  name: 'AddAdmin',
  data () {
    var validateUserName = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Username could not be null'));
        } else {
          var reUser = /^\w{5,20}$/;
          if(reUser.test(value))
          {
            callback()
          }else{
             callback(new Error('5 to 20'));
          }
        }
    }; 
    var validatePassWord = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Key could not be null'));
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
    var validateMobile = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('Phone number could not be null'));
        } else {
          var rePhone = /^1[34578]\d{9}$/;
          if(rePhone.test(value))
          {
            callback()
          }else{
             callback(new Error('Format incorrect'));
          }
        }
    };  
    return {
      pop_show:false,
      adminForm:{
        username:'',
        password:'',
        mobile:'',
        email:'',
        list:[],
        listval:[],
        list2:[],
        listval2:[]
      },
      rulesAdminForm:{
        username: [
            { validator: validateUserName, trigger: 'blur' }
        ],
        password: [
            { validator: validatePassWord, trigger: 'blur' }
        ],
        mobile: [
            { validator: validateMobile, trigger: 'blur' }
        ]
      }
    }
  },
  methods:{
    fnGetPower(){
      this.axios.get(cons.apis + '/permission/simple/', {
          headers: {
            'Authorization': 'JWT ' + token
          },
          responseType: 'json'
      })
      .then(dat=>{
          var aList = dat.data;
          var aList2 = [];
          for(var i=0;i<aList.length;i++){
            aList2.push({key:aList[i].id,label:aList[i].name})
          }
          this.adminForm.list2 = aList2;        
      }).catch(err=>{
         console.log(err);
      });
    },
    fnGetGroup(){
      this.axios.get(cons.apis + '/permission/groups/simple/', {
          headers: {
            'Authorization': 'JWT ' + token
          },
          responseType: 'json'
      })
      .then(dat=>{
          var aList = dat.data;
          var aList2 = [];
          for(var i=0;i<aList.length;i++){
            aList2.push({key:aList[i].id,label:aList[i].name})
          }
          this.adminForm.list = aList2;        
      }).catch(err=>{
         console.log(err);
      });
    },
    submitForm(){
       this.axios.post(cons.apis + '/permission/admins/', {
              "username":this.adminForm.username,
              "password":this.adminForm.password,
              "mobile":this.adminForm.mobile,
              "email":this.adminForm.email,
              "groups":this.adminForm.listval,
              "user_permissions":this.adminForm.listval2,
            },{
            headers: {
              'Authorization': 'JWT ' + token
            },
            responseType: 'json'           
        })
        .then(dat=>{
            if(dat.status==201){
              this.$message({
                type: 'success',
                message: 'Success'
              });
              this.pop_show = false;            
              this.adminForm.name = '';
              this.adminForm.listval = [];
              this.adminForm.listval2 = [];
              this.resetForm('adminForm')
              this.$emit('fnResetTable');                        
            }
        }).catch(err=>{
           if(err.response.status==400){
               this.$message({
                  type:'info',
                  message:err.response.data.name[0]
              });
           }          
        }); 
    },
    resetForm(formName){
      this.$refs[formName].resetFields();
    }
  },
  mounted(){
    this.fnGetPower();
    this.fnGetGroup();
  }
}
</script>


