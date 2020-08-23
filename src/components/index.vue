<template>
  <div>
    用户名:<input v-model="user.name"></input>
    密码：<input v-model="user.pwd"></input>
    <p>{{msg}}</p>
    <button @click="login">登录</button>
  </div>
</template>

<script>
  export default {
    name: "index",
    data:function(){
      return{"user":{"name":"","pwd":""},msg:""}
    },
    methods:{
      login:function () {
        this.$axios.post("http://localhost:8081/login",{name:this.user.name,pwd:this.user.pwd})
          .then(response=>{
            console.log(response)
            if (response.data!=null){
              console.log("成功")
              this.$router.push({path:"/loginTrue",query:{name:this.user.name}});
            }else {
              this.msg="错误"
            }

          }).catch(error=>{
          console.log(error)
          this.msg="失败"
        });
      }
    }
  }
</script>

<style scoped>

</style>
