n<template>
  <div>
    <div style="margin: 0 0 5px 0;">
      <el-button size="small" type="success" @click="showDialog()">添加</el-button>
    </div>


    <el-table height="450" :data="cls.rows" style="width: 100%">
       <el-table-column prop="cid" label="编号">
       </el-table-column>
       <el-table-column prop="cname" label="班级">
       </el-table-column>
       <el-table-column prop="bzr.bname" label="班主任">
       </el-table-column>
       <el-table-column prop="jy.jname" label="教员">
       </el-table-column>

       <el-table-column label="操作">
         <template slot-scope="scope">
           <el-button type="primary" size="mini" @click="showDialog(scope.row)">修改</el-button>
           <el-button type="danger" size="mini" @click="deleteTeacher(scope.row.cid)">删除</el-button>
         </template>
       </el-table-column>
     </el-table>
    <div align="right" style="margin: 5px 0 0 0;">
      <el-button size="mini" type="success" @click="selectFirstPage()">首页</el-button>
      <template v-if="cls.currentPage > 1">
        <el-button size="mini" type="success" @click="selectNextPage()">上一页</el-button>
      </template>
      <template v-if="cls.currentPage < cls.lastPage">
        <el-button size="mini" type="success" @click="selectPrevPage()">下一页</el-button>
      </template>
      <el-button size="mini" type="success" @click="selectLastPage()">尾页</el-button>
    </div>
     <el-dialog title="修改/添加" :visible.sync="visibleDialog" :close-on-click-modal="false">
       <el-form :model="form">
         <input v-model="form.cid"/>
         <el-form-item label="班级">
           <el-input v-model="form.cname"></el-input>
         </el-form-item>
          <h1>班主任</h1>
           <br/>
           <el-select v-model="form.bzr.bid" placeholder="请选择">
             <el-option v-for="item in bzr" :key="item.bid" :label="item.bname" :value="item.bid">
             </el-option>
           </el-select>

         <h1>教员</h1>
           <br/>
           <el-select v-model="form.jy.jid" placeholder="请选择">
             <el-option v-for="i in jy" :key="i.jid" :label="i.jname" :value="i.jid">
             </el-option>
           </el-select>
       </el-form>
       <div slot="footer" class="dialog-footer">
         <el-button @click="visibleDialog=false">取 消</el-button>
         <el-button type="primary" @click="saveOrUpdate()">确 定</el-button>
       </div>
     </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'findAll',
    data:function(){
      return {

        form:{
          cid:null,
          cname:'',
          bzr:{
            bid:null,
            bname:'',
          },
          jy:{
            jid:null,
            jname:'',
          }
        },
        cls:[],
        visibleDialog:false,
        bzr:[],
        jy:[],
        cls:{
          keyword:'',
          pageSize:5,
          currentPage:1,
          lastPage:null,
          rows:[]
        }
      }
    },created:function(){
      this.$axios.post("http://localhost:8081/cls/findAll",this.cls)
        .then(response=>{
          //console.log(response.data);
          this.cls = response.data;
        })
        .catch(error=>{
          console.log(error);
        });

    },
    methods: {
      selectAll: function () {
        this.$axios.post("http://localhost:8081/cls/findAll",this.cls)
          .then(response => {
            //console.log(response.data);
            this.cls = response.data;
          })
          .catch(error => {
            console.log(error);
          });
      },
      showDialog:function (row) {
        //查询班主任
        this.$axios.post("http://localhost:8081/tea/listbzr")
          .then(response => {
            this.bzr= response.data;
          })
          .catch(error => {
            console.log(error);
            this.$message.error('服务器异常');
          });
        //查询教员
        this.$axios.post("http://localhost:8081/tea/listjy")
          .then(response => {
            this.jy = response.data;
          })
          .catch(error => {
            console.log(error);
            this.$message.error('服务器异常');
          });
        this.visibleDialog = true;
        if (null == row) {
          //添加
          this.form = {
            cd: null,
            cname: '',
            bzr: {
              bid: null,
              bname: ''
            },
            jy: {
              jid: null,
              jname: ''
            }
          };
        }else
        {
          console.log(row);
          //修改
          this.form=Object.assign(this.form,row);
        }
      },
      saveOrUpdate:function(){
        this.$axios.post("http://localhost:8081/cls/saveOrUpdate",{
          cname:this.form.cname,
          bzr:this.form.bzr.bid,
          jy:this.form.jy.jid,
          cid:this.form.cid
        }).then(response=> {
            if(response.data!=0){
              this.$message({
                message: '操作成功',
                type: 'success'
              });
              //关闭
              this.visibleDialog=false;
              this.selectAll();//重新查询

            }else
            {
              this.$message.error('操作失败');
            }
          })
      },

        deleteTeacher:function (cid) {
          console.log(cid);
          this.$confirm('确认删除?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$axios.delete(`http://localhost:8081/cls/delete/${cid}`)
              .then(response => {
                if (response.data == 1) {
                  this.$message({
                    message: '删除成功',
                    type: 'success'
                  });
                  this.selectAll();
                } else {
                  this.$message.error('删除失败');
                }
              }).catch(error => {
              console.log(error);
              this.$message.error('服务器出现错误');
            });

          })


        },
      selectFirstPage:function () {
        this.cls.currentPage=1;
        console.log("点击首页的pageUtil");
        console.log(this.cls);
        this.selectAll();
      },
      selectNextPage:function () {
        this.cls.currentPage=this.cls.currentPage-1;
        console.log("点击下一页的pageUtil");
        console.log(this.cls);
        this.selectAll();
      },
      selectPrevPage:function () {
        this.cls.currentPage=this.cls.currentPage+1;
        console.log("点击上一页的pageUtil");
        console.log(this.cls);
        this.selectAll();
      },
      selectLastPage:function () {
        this.cls.currentPage=this.cls.lastPage;
        console.log("点击尾页的pageUtil");
        console.log(this.cls);
        this.selectAll();
      }
      }

  }
</script>

<style scoped>

</style>
