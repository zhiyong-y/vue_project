<template>
  <transition name="fade" mode="out-in">
    <el-row class="container">
        <el-col :span="24" class="header">
            <el-col :span="2">
                <div class="tools" @click.prevent="collapse">
                    <i class="fa fa-bars icon-size"></i>
                </div>
            </el-col>
            <el-col :span="10" class="logo">
                {{sysName}}
            </el-col>
            <el-col :span="4" class="userinfo">
                <el-dropdown trigger="hover">
                    <span class="el-dropdown-link userinfo-inner"><img :src="this.sysUserAvatar" /> {{sysUserName}}</span>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item>我的消息</el-dropdown-item>
                        <el-dropdown-item>设置</el-dropdown-item>
                        <el-dropdown-item divided @click.native="logout">退出登录</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
            </el-col>
        </el-col>
        <el-col :span="24" class="main">
            <aside :class="collapsed?'menu-collapsed':'menu-expanded'">
                <!-- 导航菜单 -->
                <el-menu :default-active="$route.path" class="el-menu-vertical-demo expanded" @open="handleopen" @close="handleclose" @select="handleselect" unique-opened router v-show="!collapsed"> <!--:collapsed="collapsed"  -->
                    <template v-for="(item,index) in $router.options.routes" v-if="!item.hidden">
                        <el-submenu :index="index+''" v-if="!item.leaf" :key="index">
                            <template slot="title">
                                <i :class="item.iconCls"></i>
                                <span slot="title" class="item-title" v-if="!collapsed">{{item.name}}</span>
                            </template>
                            <el-menu-item v-for="child in item.children" :index="child.path" :key="child.path" v-if="!child.hidden">
                                <span>{{child.name}}</span>
                            </el-menu-item>
                        </el-submenu>
                        <el-menu-item v-if="item.leaf&&item.children.length>0" :index="item.children[0].path" :key="item.children[0].path">
                            <i :class="item.iconCls"></i>
                            <span slot="title" class="item-title" v-if="!collapsed">{{item.children[0].name}}</span>
                        </el-menu-item>
                    </template>
                </el-menu>
                <!--导航菜单-折叠后-->
                <ul class="el-menu el-menu-vertical-demo collapsed" v-show="collapsed" ref="menuCollapsed">
                    <li v-for="(item,index) in $router.options.routes" v-if="!item.hidden" :key="index" class="el-submenu item">
                        <template v-if="!item.leaf && item.children.length > 0">
                            <div class="el-submenu__title" style="padding-left: 20px;" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)">
                                <i :class="item.iconCls"></i>
                            </div>
                            <ul class="el-menu submenu" :class="'submenu-hook-'+index" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)">
                                <li v-for="child in item.children" v-if="!child.hidden" :key="child.path" class="el-menu-item" :class="$route.path==child.path?'is-active':''" @click="$router.push(child.path)">
                                    <span slot="title" class="item-title" >{{child.name}}</span>
                                </li>
                            </ul>
                        </template>
                        <template  v-if="item.leaf">
                          <div class="el-submenu__title" style="padding-left: 20px;height: 56px;line-height: 56px;padding: 0 20px;" :class="$route.path==item.children[0].path ? 'is-active':''" @click="$router.push(item.children[0].path)">
                                <i :class="item.iconCls"></i>
                          </div>
                        </template>
                    </li>
                </ul>
            </aside>
            <section class="content-container">
                <div class="grid-content bg-purple-light">
                    <el-col :span="24" class="breadcrumb-container">
                        <strong class="title">{{$route.name}}</strong>
                        <el-breadcrumb separator="/" class="breadcrumb-inner">
                            <el-breadcrumb-item v-for="item in $route.matched" :key="item.path">
                                {{ item.name }}
                            </el-breadcrumb-item>
                        </el-breadcrumb>
                    </el-col>
                    <el-col :span="24" class="content-wrapper">
                        <transition name="fade" mode="out-in">
                            <router-view></router-view>
                        </transition>
                    </el-col>
                </div>
            </section>
        </el-col>
    </el-row>
  </transition>
</template>

<script>
export default {
  data () {
    return {
      sysName: '',
      collapsed: false,
      sysUserName: '',
      sysUserAvatar: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560153562&di=f9eefca8976b83a8d6db85b2712077e1&imgtype=jpg&er=1&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201612%2F08%2F20161208204750_rS8N4.jpeg',
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      }
    }
  },
  methods: {
    onSubmit () {
      console.log('submit!')
    },
    handleopen () {
      // console.log('handleopen')
    },
    handleclose () {
      // console.log('handleclose')
    },
    handleselect: function (a, b) {
    },
    // 退出登录
    logout: function () {
      var _this = this
      this.$confirm('确认退出吗?', '提示', {
        type: 'warning'
      }).then(() => {
        sessionStorage.removeItem('user')
        _this.$router.push('/login')
      }).catch(() => {})
    },
    // 折叠导航栏
    collapse () {
      this.collapsed = !this.collapsed
    },
    showMenu (i, status) {
      this.$refs.menuCollapsed.getElementsByClassName('submenu-hook-' + i)[0].style.display = status ? 'block' : 'none'
    }
  },
  mounted () {
    var user = sessionStorage.getItem('user')
    if (user) {
      user = JSON.parse(user)
      this.sysUserName = user.name || ''
      this.sysUserAvatar = user.avatar || ''
    }
  }
}
</script>
<style scoped>
.icon-size {
    font-size: 20px;
}
.container {
    position: absolute;
    top: 0px;
    bottom: 0px;
    width: 100%;
}
.container .header {
    height: 60px;
    line-height: 60px;
    background: #20a0ff;
    color:#fff;
}
.container .header .tools{
    margin: 15px;
    width: 30px;
    height: 30px;
    line-height: 35px;
    cursor: pointer;
    text-align: center;
}
.container .header .logo-collapse-width{
    width:60px;
    opacity: 0.2;
}
.container .header .logo-width{
    width:230px;
    opacity: 1;
}
.container .header .userinfo {
    text-align: right;
    padding-right: 35px;
    float: right;
}
.container .header .userinfo-inner {
    cursor: pointer;
    color:#fff;
}
.container .header .userinfo-inner img {
    width: 40px;
    height: 40px;
    border-radius: 20px;
    margin: 10px 0px 10px 10px;
    float: right;
}
.container .header .logo {
    height:60px;
    font-size: 18px;
    padding-right:20px;
}
.container .header .logo img {
    width: 40px;
    float: left;
    margin: 10px 10px 10px 18px;
}
.container .header .logo .txt {
    color:#fff;
}
.container .main {
    display: flex;
    position: absolute;
    top: 60px;
    bottom: 0px;
    overflow: hidden;
}
.container .main .content-container {
    flex:1;
    overflow-y: scroll;
    padding: 20px;
}
.container .main .content-container .content-wrapper {
    background-color: #fff;
    box-sizing: border-box;
}
.container .main .content-container .breadcrumb-container .breadcrumb-inner{
    float: right;
}
.container .main .content-container .breadcrumb-container .title {
    width: 200px;
    float: left;
    color: #475669;
}
.container .main .menu-expanded{
    flex:0 0 230px;
    width: 230px;
}
.container .main .menu-collapsed{
    flex:0 0 65px;
    width: 65px;
}
.item-title {
    width: 100%;
    height: 100%;
    display: inline-block;
    padding-left: 20px;
    box-sizing: border-box;
}
.container .main aside {
    flex:0 0 230px;
    width: 230px;
}
.container .main aside .el-menu{
    width: 100% !important;
    height: 100%;
}
.container .main aside .collapsed{
    width:65px;
}
.container .main aside .collapsed .item{
    position: relative;
}
.container .main aside .collapsed .submenu{
    position:absolute;
    top:0px;
    left:65px;
    z-index:99999;
    min-width: 210px;
    height: auto;
    display:none;
    box-shadow: 0 0 10px rgba(0,0,0, .15);
    border-radius: 4px;
}
.submenu li {
    padding: 0;
    height: 56px;
    line-height: 56px;
}
i {
    font-size: 18px;
}
.submenu .el-menu-item:hover {
    width: 210px;
    padding-left: 2px;
    transition: padding-left ease .5s;
}
.submenu .el-menu-item {
    transition: padding-left ease .5s;
}
</style>
