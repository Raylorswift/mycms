<template>
  <div class="user">
    <!-- {{multipleSelection}} -->
    <div class="btn">
      <el-button type="success" @click='toAddUser' plain>新增</el-button>
    <!-- <el-button type='success' @click='batchDelete'>批量删除</el-button> -->
    </div>
    
    <el-table :data="users" style="width: 100%" :border='true' @selection-change="handleSelectionChange" v-loading='loading'>
      <!-- <el-table-column
        type="selection"
        width="55">
      </el-table-column> -->
      <el-table-column
        prop="id"
        width="130"
        label="学号">
      </el-table-column>
      <el-table-column
        prop="username"
        label="姓名"
        width="150">
      </el-table-column>
      <el-table-column
        prop="nickname"
        label="用户名"
        width="150">
      </el-table-column>
      <el-table-column
        prop="email"
        label="邮箱">
      </el-table-column>

      <el-table-column
        prop="regTime"
        label="提交时间"
        width="240">
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope='{row}'>
          <!-- <el-button type="text" @click="deleteUser(row.id)">删除</el-button> -->
                    <i class="fa fa-trash" @click='deleteUser(row.id)'></i>
                    <i class="fa fa-pencil" @click='toUpdateUser(row)'></i>
        </template>
      </el-table-column>
    </el-table>

    <!-- 模态框 -->
    <el-dialog :title="uDialog.title" :visible.sync="uDialog.visible">
      <el-form :model="uDialog.form" size="mini" label-position="left" >
        <el-form-item label="姓名" label-width="6em">
          <el-input v-model="uDialog.form.username" autocomplete="off"></el-input>
        </el-form-item>
         <el-form-item label="学号" label-width="6em">
          <el-input v-model="uDialog.form.id" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="pass" label-width="6em">
          <el-input type="password" v-model="uDialog.form.password" autocomplete="off"></el-input>
        </el-form-item>
         <el-form-item label="用户名" label-width="6em">
          <el-input v-model="uDialog.form.nickname" autocomplete="off"></el-input>
        </el-form-item>
         <el-form-item label="邮箱" label-width="6em">
          <el-input v-model="uDialog.form.email" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size='mini' @click='closeUDialog'>取 消</el-button>
        <el-button type="primary" size='mini' @click='saveOrUpdateUser'>确 定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
  import axios from '@/http/axios'
  export default {
    data(){
      return {
        loading:false,
        users:[],
        multipleSelection:[],
        uDialog:{
          title:'',
          visible:false,
          form:{}
        }
      }
    },
    created(){
      //查询所有学生信息
      this.loadAllUsers();
    },
    methods:{
       /* 弹出新增模态框 */ 
      toAddUser(){
        this.uDialog.title = '新增学生信息';
        this.uDialog.visible = true;
        this.uDialog.form = {};
      }, 
      /* 弹出修改模态框 */ 
      toUpdateUser(row){
        this.uDialog.title='修改学生信息';
        this.uDialog.form=row;
        this.uDialog.visible = true;
      },
      /* 提交保存或者更新学生表单 */
      saveOrUpdateUser(){
        axios.post('/manager/user/saveOrUpdateUser',this.uDialog.form)
        .then(()=>{
          this.$notify.success({
            title:'成功',
            message:'提交成功！'
          });
          this.closeUDialog();
          this.loadAllUsers();
        })
        .catch(()=>{
          this.$notify.error({
            title:'错误',
            message:'提交失败！'
          });
        });
      },
      /* 关闭模态框 */ 
      closeUDialog(){
        this.uDialog.form = {};
        this.uDialog.visible = false;
      },
          
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      /* 通过id删除学生 */ 
      deleteUser(id){
        this.$confirm('此操作将永久删除该数据，是否继续？','提示',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          axios.get('/manager/user/deleteUserById?id='+id)
          .then(()=>{
            this.$notify.success({
              title:'成功',
              message:'删除成功！'
            });
            this.loadAllUsers();
          }).catch(()=>{
            this.$notify.error({
              title:'错误',
              message:'删除失败！'
            });
          });
        })
      },
      /* 加载学生信息  */ 
      loadAllUsers(){
        axios({
          baseURL:'http://47.106.188.95:8099',
          url:'/manager/user/findAllUser',
          method:'get'
        }).then(({data:result})=>{
          this.users = result.data;
        }).catch(()=>{
          this.$notify.error({
            title: '错误',
            message: '网络异常！'
          });
        })
        .finally(()=>{
          this.loading=false;
        })
      }
    }
  }
</script>
<style>
  .btn{
    margin-bottom: .5em;
  }
  i.fa {
    margin: 0 .5em;
    cursor: pointer;
  }
  i.fa.fa-trash{
    color: #F56C6C;
  }
</style>