<template>
    <header class="header-nav">
        <el-row>
            <el-col :xs="7" :sm="3" :md="3" :lg="2" :offset="1">
                <div class="header">
                    <img src="../assets/icon.png" alt="">
                    <h1>众筹</h1>
                </div>
            </el-col>
            <el-col :xs="18" :sm="20" :md="20" :lg="21" class="offcanvas-collapse">
                <el-col :xs="23" :sm="19" :md="15" :lg="18">
                    <el-menu :default-active="this.$router.path" router mode="horizontal">
                        <el-menu-item v-for="(item,i) in navList" :key="i" :index="item.name">
                            {{ item.navItem }}
                        </el-menu-item>
                    </el-menu>
                </el-col>
                <el-col :xs="6" :sm="6" :md="8" :lg="7" class="rightNav">
                    <div>
                        <el-input placeholder="search" v-model="searchCont"><i slot="suffix" class="el-input__icon el-icon-search" @click="search"></i></el-input>
                    </div>
                    <div class="lo-re-box">
                        <router-link to="/login" v-if="!this.$store.state.user.name">登录</router-link>
                        <div class="isLogin" v-else>
                            <img :src="this.$store.state.user.img" >
                            <el-dropdown trigger="click">
                                <span class="el-dropdown-link">
                                    <span class="username">{{ this.$store.state.user.name }}</span> <i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu slot="dropdown">
                                    <el-dropdown-item><span @click="loginOut">注销</span></el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                        <!-- <span>|</span>
                        <a href="javascrit:;">注册</a> -->
                    </div>
                </el-col>
            </el-col>
            <el-col :xs="1" :sm="1" :md="1" :lg="1">
                <span class="nav-on" @click="navUp"><i></i><i></i><i></i></span>
            </el-col>
                <el-menu
                :default-active="this.$router.path"
                router
                :class="{'collaps-nav':true,'navActive':navActive}">
                <el-menu-item v-for="(item,i) in navList" :key="i" :index="item.name" @click="navActive = false">
                    <span slot="title">{{ item.navItem }}</span>
                </el-menu-item>
                <el-menu-item index="1">
                    <div slot="title" class="mobileRightNav">
                        <el-input placeholder="search" v-model="searchCont"><i slot="suffix" class="el-input__icon el-icon-search" @click="search"></i></el-input>
                    </div>
                </el-menu-item>
                <el-menu-item index="2">
                    <div class="lo-re-box-mobile" slot="title">
                        <router-link to="/login" v-if="!this.$store.state.user.name" @click="navActive = false">登录</router-link>
                        <div class="isLogin" v-else>
                            <img :src="this.$store.state.user.img" >
                            <el-dropdown trigger="click">
                                <span class="el-dropdown-link">
                                    <span class="username">{{ this.$store.state.user.name }}</span>
                                </span>
                            </el-dropdown>
                        </div>
                    </div>
                </el-menu-item>
            </el-menu>
        </el-row>
    </header>
</template>
<script>
// import {mapState,mapGetters,mapActions,mapMutations} from '../vuex'
export default {
    data() {
      return {
        navList:[
            {name:'/findProject',navItem:'发现项目'},
            {name:'/communityActivity',navItem:'社区动态'},
            {name:'/publishProject',navItem:'发布项目'},
            {name:'/personalCenter',navItem:'个人中心'},
            {name:'/manageCenter',navItem:'管理员中心'},
        ],
        navActive:false,
        searchCont:''
      };
    },
    methods: {
        pathTo (url) {
            this.$router.push(url)
        },
        loginOut(){
            this.$store.commit('LOGIN_OUT')
        },
        navUp(){
            this.navActive = !this.navActive
        },
        search(){
            if(this.searchCont){
                this.$axios.post('/search',{
                    searchCont:this.searchCont
                }).then(res => this.$router.push('/search/'+res.data)).catch(err => this.$message.error('搜索失败'))
            }else{
                this.$message.warning('搜索内容不能为空')
            }
        }
    }
}
</script>
<style scoped>
header{
    border-bottom: 1px solid #ccc;
    background: white;
    width: 100%;
    position: fixed;
    z-index:10002;
}
.header,.lo-re-box,.rightNav{
    display: flex;
    flex-direction: row;
    align-items: center;
}
.header img{
    width:40px;
}
header h1{
    font-size: 22px;
    color: rgb(119, 111, 111);
}
.el-menu{
    border:none;
}
.el-menu-item a{
    font-size: 15px;
    font-weight: 600;
    text-decoration: none;
}
.rightNav{
    position: absolute;
    right: 20px;
    height: 60px;
}
.lo-re-box{
    margin-left: 20px;
}
.rightNav a{
    font-size: 14px;
    width: 30px;
    text-decoration: none;
    color: rgb(4, 61, 168);
}
.rightNav a:hover{
    text-decoration: underline blue;
}
.isLogin{
    display: flex;
    flex-direction: row;
    align-items: center;
}
.isLogin img{
    width: 30px;
    border-radius: 50%;
    margin-right: 10px;
}
.el-dropdown .username{
    display: inline-block;
    width: 60px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    cursor: pointer;
}
.lo-re-box-mobile .username{
    width: 100%;
}
span.nav-on{
    display:none;
    width:25px;
    position: absolute;
    top:20px;
    right:12px;
    cursor: pointer;
}
.nav-on i{
    display:block;
    width:100%;
    height:2px;
    background:rgb(137, 167, 192);
    border-radius: 1px;
    margin-bottom:5px;
} 
.collaps-nav{
    margin-top: 60px;
    display: none;
}
@media screen and (max-width: 768px) {
    .offcanvas-collapse{
        display: none;
    }
    .navActive{
        display: block;
    }
    span.nav-on{
        display: block;
    }
    .header h1{
        width:60px;
    }
}
</style>
