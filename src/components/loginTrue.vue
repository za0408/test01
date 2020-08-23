<template>
  <div>
    <el-container>
      <el-header height="60px">
        <el-dropdown trigger="click" @command="">
                    <span class="el-dropdown-link">
                        <i class="el-icon-s-custom"></i>
                        张三
                        <i class="el-icon-arrow-down"></i>
                    </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="Personal">个人信息</el-dropdown-item>
            <el-dropdown-item command="Editpwd">修改密码</el-dropdown-item>
            <el-dropdown-item command="Layout">退出</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-header>
      <el-container>
        <el-aside>
          <!-- default-active:默认激活 -->
          <!-- router:是否使用路由 -->
          <el-menu default-active="2-1" :router="true">
            <!-- 使用路由之后，item就被当作router-link来使用 -->
            <el-menu-item index="1" :route="{name:'Personal'}">
              <i class="el-icon-user-solid"></i>
              <span>用户管理</span>
            </el-menu-item>

            <el-submenu index="2">
              <template slot="title">
                <i class="el-icon-apple"></i>
                <span>基础管理</span>
              </template>
              <el-menu-item index="2-1" :route="{name:'Emp'}">
                <i class="el-icon-camera"></i>
                <span>员工管理</span>
              </el-menu-item>
              <el-menu-item index="2-2">
                <i class="el-icon-position"></i>
                <span>部门管理</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="3">
              <template slot="title">
                <i class="el-icon-apple"></i>
                <span>报表管理</span>
              </template>
              <el-menu-item index="3-1">
                <i class="el-icon-camera"></i>
                <span>日报表</span>
              </el-menu-item>
              <el-menu-item index="3-2">
                <i class="el-icon-position"></i>
                <span>月报表</span>
              </el-menu-item>
            </el-submenu>

          </el-menu>
        </el-aside>
        <el-main>
          <el-table width="100%" border :data="this.$route.query.list" stripe height="600px">
            <el-table-column label="编号" prop="sid" width="50px"></el-table-column>
            <el-table-column label="名称" prop="sname"></el-table-column>
            <el-table-column label="班级编号" prop="cid"></el-table-column>
            <el-table-column>
              <template slot-scope="scope">
            <el-button type="text" @click="showDialog(scope.row)">修改</el-button>
            <el-button type="text" @click="del(scope.row)">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
          <!-- 路由视图 -->
          <router-view>

          </router-view>
        </el-main>
      </el-container>
      <el-footer height="30px">
        版权所有，违法必究
      </el-footer>
    </el-container>
    <el-dialog width="50%" title="修改员工" :visible.sync="visibleDialog">
      <el-form label-width="100px" label-suffix="：" :model="student">
        <el-form-item label="id">
          <el-input v-model="student.sid"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="student.sname"></el-input>
        </el-form-item>
        <el-form-item label="班级编号">
          <el-input v-model="student.cid"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button type="primary" >提交</el-button>
        <el-button @click="visibleDialog = false">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
    export default {
        name: "loginTrue",
      data:function () {
        return{ visibleDialog:false,student:{},list:this.$route.query.list}
      },methods:{
          showDialog:function (data) {
            this.visibleDialog = true;
            this.student = data;
            if(null == data){
              this.student = {};
            }else{
              console.info(data)
              this.student = Object.assign({},data);
            }
          },del:function (data) {
          this.$axios("http://localhost:8081/student/delete",{"student":data}).then(province=>{
          });
        }
      }
    }
</script>

<style scoped>

</style>
