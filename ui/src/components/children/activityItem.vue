<template>
    <el-row class="one">
        <el-row>
            <el-col class="authorBox">
                <img @click="goToPersonPage(item.userName)" :src="item.userImg" alt="">
                <div>
                    <span @click="goToPersonPage(item.userName)">{{ item.userName }}</span>
                    <span>{{ timeFn(item.publishTime) }} · 发布{{ item.publishType }}</span>
                </div>
            </el-col>
        </el-row>
        <el-row v-if="item.publishType=='动态'">
            <span class="content" @click="goToProject(item.id)">
                {{ item.publishContent }}
            </span>
            <div v-if="item.img.length>0" class="photoWall">
                <el-row v-if="item.img.length == 1">
                    <el-col :span="24">
                        <img :src="item.img[0]" @click="enlargeImg(item.img[0])" />
                    </el-col>
                </el-row>
                <el-row v-else-if="item.img.length > 1&&item.img.length<=4">
                    <el-col :span="12" v-for="(item,i) in item.img" :key="i">
                        <img :src="item" alt=""  @click="enlargeImg(item)">
                    </el-col>
                </el-row>
                <el-row v-else-if="item.img.length > 4 && item.img.length <= 9">
                    <el-col :span="8" v-for="(item,i) in item.img" :key="i">
                        <img :src="item" alt="" @click="enlargeImg(item)">
                    </el-col>
                </el-row>
                <el-row v-else-if="item.img.length > 9">
                    <el-col :span="8" v-for="(item,i) in item.img" :key="i" v-if="i < 8">
                        <img :src="item" alt="" @click="enlargeImg(item)" >
                    </el-col>
                </el-row>
            </div>
        </el-row>
        <el-row v-if="item.publishType=='长文'">
            <span class="content" @click="goToProject(item.id)">
                {{ item.publishContent }}
            </span>
        </el-row>
        <el-row v-if="item.publishType=='投票'" class="vote">
            <span class="content" @click="goToProject(item.id)">
                {{ item.publishContent }}
            </span>
            <el-checkbox-group v-model="item.voteRes">
                <el-checkbox v-if="item.statu == '投票'" :label="option" name="type" v-for="(option,i) in item.options" :key="i"></el-checkbox>
                <el-checkbox v-else :label="option" name="type" disabled v-for="(option,i) in item.options" :key="i"></el-checkbox>
            </el-checkbox-group>
            <div class="voteBox">
                <span>可选数: {{ item.canSelectNum }}</span>
                <span>当前选择数: {{ item.voteRes.length }}</span>
                <el-button type="primary" @click="vote(item.id,item.canSelectNum,item.voteRes)">{{ item.statu }}</el-button>
                <p>你的投票结果为：</p>
                <ul>
                <li v-for="(val,i) in item.voteRes" :key="i"> {{ val }}</li>
                </ul>
            </div>
        </el-row>
        <el-row class="operations">
            <span><i class="fa fa-share"></i>分享</span>
            <span @click="goToProject(item.id)"><i class="fa fa-comment"></i>{{item.commentNum}}条评论</span>
            <span @click="addZan(item.id,'')"><i class="fa fa-thumbs-up" :class="{zanActive:item.zanActive}"></i>{{ item.supportNum }}赞</span>
            <span @click="goToProject(item.id)" v-if="item.publishType=='长文'"><i class="fa fa-eye"></i>查看全文</span>
        </el-row>
        <el-row class="comment">
            <el-row  v-for="(val,i) in item.comments" :key="i">
                <el-row class="com">
                    <el-col :span="2">
                        <img class="headImg" :src="val.comImg">
                    </el-col>
                    <el-col :span="22">
                        <span>{{ val.comName }}</span>
                        <span>{{ val.comTime }}</span>
                        <p>{{ val.comCont }}</p>
                        <div class="box">
                            <div><img :src="require('../../assets/评论.png')"><sup>{{ val.reply.length }}</sup></div>
                            <div @click="addZan(item.id,val.comId)">
                                <img v-show="!val.zanActive" :src="require('../../assets/zan.png')">
                                <img v-show="val.zanActive" :src="require('../../assets/赞.png')">
                                <sup>{{ val.supportNum }}</sup>
                            </div>
                        </div>
                    </el-col>
                </el-row>
            </el-row>
            <el-row class="moreComment" v-if="page === 'communityActivity'">
                <span v-show="item.comments.length > 3" @click="goToProject(item.id)">查看全部{{ item.commentNum }}条评论<i class="el-icon-caret-right"></i></span>
            </el-row>
            <el-row class="myCom">
                <el-col :span="23">
                    <img class="headImg" :src="this.$store.state.user.img" alt="">
                    <el-input :placeholder="placehodlder" @focus="isLogin" v-model="comCont"></el-input>
                    <i class="fa fa-arrow-right" @click="pubCom(item.id,'',comCont)"></i>
                </el-col>
            </el-row>
        </el-row>
    </el-row>
</template>
<script>
export default {
    props: {
        item:Object,
        page:String
    },
    data () {
        return {
            placehodlder:'',
            comCont:''
        }
    },
    computed:{
        _voteRes:{
            get:() => {
                if(!isNaN(this.item.canSelectNum)){
                    return this.item.voteRes.splice(0,(this.item.canSelectNum - 1))
                }
            }
        }
    },
    methods: {
        timeFn(d1) {//d1作为一个变量传进来
            //如果时间格式是正确的，那下面这一步转化时间格式就可以不用了
            var dateBegin = new Date(d1.replace(/-/g, "/"));//将-转化为/，使用new Date
            var dateEnd = new Date();//获取当前时间
            var dateDiff = dateEnd.getTime() - dateBegin.getTime();//时间差的毫秒数
            var dayDiff = Math.floor(dateDiff / (24 * 3600 * 1000));//计算出相差天数
            var leave1=dateDiff%(24*3600*1000)    //计算天数后剩余的毫秒数
            var hours=Math.floor(leave1/(3600*1000))//计算出小时数
            //计算相差分钟数
            var leave2=leave1%(3600*1000)    //计算小时数后剩余的毫秒数
            var minutes=Math.floor(leave2/(60*1000))//计算相差分钟数
            if(dayDiff === 0 && hours === 0){
                return minutes + '分钟前'
            }else if(dayDiff === 0 && hours !== 0){
                return hours + '小时前'
            }else if(dayDiff === 1){
                return '昨天'
            }else if(dayDiff === 2){
                return '前天'
            }else{
                return d1.slice(0,10)
            }
        },
        enlargeImg(url){
            const body = document.getElementsByTagName('body')[0]
            const cover = document.createElement('div')
            cover.className = "enlarge"
            cover.style.width = document.body.clientWidth+'px'
            cover.style.height = document.body.clientHeight+'px'
            body.appendChild(cover)
            const img = document.createElement('img')
            img.className = 'enlargeImage'
            img.src = url
            cover.appendChild(img)
            cover.onclick = () => {
                body.removeChild(cover)
            }
        },
        isLogin(){
            if(!this.$store.state.user.name){
                this.$router.push('/login')
            }
        },
        goToPersonPage(name){
            this.$router.push('/user/'+name)
        },
        goToProject(id){
            this.$router.push('/project/'+id)
        },
        addZan(itemId,comId){
            if(comId === ''){
                this.item.zanActive = !this.item.zanActive
                if(this.item.zanActive){
                    this.item.supportNum ++
                }else{
                    this.item.supportNum --
                }
                clearTimeout(this.item.timer)
                this.item.timer = setTimeout(() => {
                    this.$axios.post('/addZan',{
                        itemId:itemId,
                        comId:comId,
                        add:this.item.zanActive
                    }).then(res => {
                        this.$emit('theLatest',res.data)
                    })
                },2000)
            }else{
                this.item.comments[comId].zanActive = !this.item.comments[comId].zanActive
                if(this.item.comments[comId].zanActive){
                    this.item.comments[comId].supportNum ++
                }else{
                    this.item.comments[comId].supportNum --
                }
                clearTimeout(this.item.comments[comId].timer)
                this.item.comments[comId].timer = setTimeout(() => {
                    this.$axios.post('/addZan',{
                        itemId:itemId,
                        comId:comId,
                        add:this.item.zanActive
                    }).then(res => {
                        console.log('b')
                        this.$emit('theLatest',res.data)
                    })
                },2000)
            }
        },
        pubCom(id,comId,text){
            if(!this.$store.state.user.name){
                this.$router.push('/login')
            }else if(!text){
                this.$message({
                    type:'info',
                    message:'评论不能为空'
                })
            }else{
                this.$axios.post('/pubCom',{
                    itemId:id,
                    comId:comId,
                    comCont:text
                }).then(res => {
                    this.$emit('theLastest',res.data)
                })
            }
        },
        comInputShow(id){
            this.item.comments[id].inputShow = !this.item.comments[id].inputShow
        },
        vote(id,num,res){
            if(res.length === num){
                this.$axios.post('/vote',{
                    itemId:id,
                    voteRes:this.item.voteRes
                }).then(res => {
                    this.$message({
                        type:'success',
                        message:'投票成功'
                    })
                    this.$emit('theLastest',res.data)
                }).catch(err => {
                    this.$message({
                        type:'warning',
                        message:'投票失败，请重试'
                    })
                })
            }else{
                this.$message.warning('请选择'+num+'个')
            }
        }
    },
    mounted () {
        if(!this.$store.state.user.name){
            this.placehodlder = '请登录后发表评论'
        }
    }
}
</script>
<style lang="scss" scoped>
$black:black;
@mixin flex-style {
    display: flex;
    flex-direction: row;
    align-items: center;
}
.zanActive{
    color: rgb(211, 61, 41)
}
img{
    cursor: pointer;
}
.one {
    margin-top: 30px;
    color:$black;
    .authorBox {
        @include flex-style;
        >img {
            width: 40px;
            border-radius: 50%;
        }
        >div {
            margin-left: 10px;
            & :first-child {
                font-weight: 600;
                color: $black;
                cursor: pointer;
            }
            & :nth-child(2) {
                font-size: 14px;
                display: block;
                margin-top: 5px;
            }
        }
    }
    .content{
        display: inline-block;
        color: $black;
        cursor: pointer;
        margin:10px 0;
    }
    ul{
        font-size: 14px;
        li{
            margin-top: 5px;
        }
    }
    .photoWall {
        .el-col {
            padding: 5px;
            img {
                width: 100%;
            }
        }
    }
    .vote{
        // position: relative;
        .voteBox{
            margin: 10px 0;
            .el-button{
                margin-left: 20px;
            }
        }
        .el-checkbox{
            display: block;
            margin-left: 0;
            margin: 10px 10px;
        }
    }
    .operations {
        color:rgb(175, 174, 174);
        font-size: 14px;
        margin: 10px 0;
        >span {
            margin-right: 20px;
            cursor: pointer;
            >i {
                margin-right: 5px;
            }
        }
    }
    .comment {
        margin: 30px 0;
        padding: 20px;
        background: rgba(241, 241, 241, 0.438);
        .com{
            position:relation;
            font-size: 0.9em;
            .headImg{
                width:30px;
                margin: 0 auto;
                border-radius:50%;
            }
            .el-col:nth-child(1){
                text-align:center;
            }
            .el-col:nth-child(2){
                &>span:first-child{
                    font-weight: 600;
                    @media screen and (max-width: 667px) {
                        margin-left: 8px;
                    }
                }
                &>span:nth-child(2){
                    margin-top:5px;
                    font-size:0.8em;
                    display: block;
                    color:rgb(139, 136, 136);
                    @media screen and (max-width: 667px) {
                        margin-left: 8px;
                    }
                }
                .box{
                    position:absolute;
                    right:0;
                    top:0;
                    div{
                        margin-right:20px;
                        display:inline-block;
                        @media screen and (max-width: 667px) {
                            margin-right:10px;
                        }
                        img{
                            width:20px;
                            opacity:0.8;
                            filter:opacity(80%);
                            margin-right: 2px;
                            @media screen and (max-width: 667px) {
                                width:15px;
                            }
                        }
                    }
                }
                // .addCom{
                //     color:rgb(139, 136, 136);
                //     font-size:0.9em;
                //     background:rgba(234, 237, 238, 0.616);
                //     padding:20px;
                //     p{
                //         margin-top:0;
                //         &>span{
                //             color:#000;
                //             font-weight:600;
                //         }
                //     }
                //     &>div{
                //         position:relative;
                //         img{
                //             position:absolute;
                //             right:0;
                //             top:0;
                //             width:20px;
                //             opacity:0.8;
                //             filter:opacity(80%);
                //         }
                //     }
                // }
            }
        }
        .moreComment {
            font-size: 16px;
            text-align: center;
            margin: 20px 0;
            cursor: pointer;
            span{
                color: #4d7da5;
                text-decoration: none;
            }
        }
        .myCom {
            .headImg{
                width:30px;
                margin-right: 5px;
                border-radius: 50%;
            }
            >.el-col {
                @include flex-style;
                i {
                    position: relative;
                    left: -25px;
                    top: 3px;
                    cursor: pointer;
                }
            }
        }
        .comInput{
            margin-top: 10px;
            >.el-col{
                position: relative;
                .fa-arrow-right{
                    position: absolute;
                    right: 10px;
                    top: 10px;
                    cursor: pointer;
                }
            }
        }
    }
}
</style>

