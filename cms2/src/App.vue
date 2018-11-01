<template>
  <div class="app">
    <div class="header">
      <div class="title">看点咨询精选</div>
    </div>
    <div class="content">
      <div class="left_nav">
        <ul>
          <li :class="{current:currentRoute=='/'}">
            <router-link to='/'>欢迎页面</router-link>
          </li>
          <li :class="{current:currentRoute=='/category'}">
            <router-link to='/category'>栏目管理</router-link>
          </li>
          <li :class="{current:currentRoute=='/article'}">
           <!--  <router-link to='/article'>文章管理</router-link> -->
           <a href="javascript:void(0)" @click='jump("/article")'>文章管理</a>
          </li>
          <li :class="{current:currentRoute=='/user'}">
             <router-link to='/user'>用户管理</router-link>
          </li>
          <li :class="{current:currentRoute=='/exam'}">
             <router-link to='/exam'>考试管理</router-link>
          </li>
         
        </ul>

        
        
      </div>
      <div class="right_content">
        <div class="wrapper">
          <router-view></router-view>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    methods:{
      jump(url){
        // alert(url);
        this.$router.push(url)
      }
    },
    mounted(){
      // this.$router.push('/user');
      
    },
    watch:{
      '$route':function(to,from){
        this.currentRoute = to.path;
      }
    },
    data(){
      return {
        msg:'App.vue',
        currentRoute:'/'
      }
    }
  }
</script>

<style>
  html{
    color:#666;
    font:normal normal 20px '微软雅黑'/*cursive*/;
  }
  body,ul,ol,dl,p,h1,h2,h3{
    margin:0;
    padding: 0;
    /*解决左右混动条*/
  }
  ul,li{
    list-style: none;
  }
  a{
    text-decoration: none;
    color: black
  }
  .wrapper{
    overflow-y: auto;
  }
  .header{
    position: absolute;
    width: 100%;
    height: 60px;
    top:0px;
    background-color:teal;
  }
  .header .title{
    color: #ffffff;
    line-height: 60px;
    padding: 0 1.5em;
   font-size: 25px;
   font-weight: 780;
   font-family:cursive;
  }
  .content{
    position: absolute;
    width: 100%;
    top:60px;
    bottom: 0px;
  }
 .content > .left_nav{
    width: 160px;
    height: 100%;
    float: left; 
  }
  .left_nav ul{

  }
  .left_nav ul li{
    line-height: 2.4em;
    text-align: center;
    border-bottom: 1px solid #ededed;
  }
  .left_nav ul li.current {
    background-color: #f0f0f0;
  }
  .content >.right_content{
    box-sizing: border-box;
    margin-left:160px;
    height: 100%;
    background-color: #f0f0f0;
    padding: .5em;
    font-size: 2em;
  }
  .content >.right_content >.wrapper{
    box-sizing: border-box;
    width:100%;
    height: 100%;
    border-radius:5px;
    background-color: #ffffff;
    padding: .5em;
    overflow-y: auto;
  }

</style>



