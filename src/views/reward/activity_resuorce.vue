<template>
    <section>
        <div class="ecrw-in">
            <div class="ecrw-zhengji-upload clearfix">
                <div class="ecrw-zhengji-upload-left">
                    <div class="ecrw-zhengji-upload-wrap">
                        <div class="ecrw-zhengji-upload-wrap-tab">
                            <ul class="clearfix">
                                <li class="ecrw-zhengji-upload-wrap-tab-li ecrw-zhengji-upload-wrap-tab-sel">
                                    <a href="javascript:;" @click="goPage('/reward')">资源悬赏列表</a>
                                </li>
                                <li class="ecrw-zhengji-upload-wrap-tab-li">
                                    <a href="javascript:;" @click="goPage('/reward/create')" v-if="ECRUtil.isLoggedIn()">我发起的</a>
                                </li>
                                <li class="ecrw-zhengji-upload-wrap-tab-li">
                                    <a href="javascript:;" @click="goPage('/reward/join')" v-if="ECRUtil.isLoggedIn() && ECRUtil.isTeacher()">我参与的</a>
                                </li>
                            </ul>
                        </div>
                        <div class="ecrw-zhengji-upload-list">
                            <div class="ecrw-content-title ecrw-content-title-xuanshang">
                                资源悬赏列表
                            </div>
                            <div class="ecrw-zhengji-upload-answer" v-loading="listLoading">
                                <div class="ecrw-exam-content-wrap ecrw-show">
                                    <div v-if="total == 0" style="text-align:  center; font-size: 16px; margin-top: 20px;">没有资源!
                                    </div>
                                    <div v-for="(item, idx) in activity_goods" :key="idx" :data="item">
                                        <div class="ecrw-exam-list clearfix">
                                            <div class="ecrw-exam-list-left" :class="activity_goods_class[idx].name">
                                                <img :src="activity_goods_class[idx].path.localpath" v-if="activity_goods_class[idx].path.localpath!=''" style="height: 100px; width: 90px" />
                                            </div>
                                            <div class="ecrw-exam-list-center">
                                                <div class="ecrw-exam-list-desc">
                                                    <ul>
                                                        <li class="ecrw-exam-list-title">
                                                            <a href="javascript:;" class="ecrw-exam-list-title-href">
                                                                <span class="ecrw-exam-list-title-gray" v-if="item.activitystatus == 0">已结束</span>
                                                                <span class="ecrw-exam-list-title-span" v-if="item.activitystatus == 1">待解决</span>
                                                                <span class="ecrw-exam-list-title-gray" v-if="item.activitystatus == 2">已解决</span>
                                                                {{item.name}}
                                                            </a>
                                                        </li>
                                                        <li class="ecrw-exam-list-det">
                                                            {{item.descriptiontext}}
                                                        </li>
                                                        <li class="ecrw-exam-list-other">
                                                            <span class="ecrw-exam-list-other-span">
                                                                上传时间：{{ ECRUtil.formatDate.format(new Date(item.createtime), 'y/M/d') }}
                                                            </span>
                                                            <span class="ecrw-exam-list-other-span">
                                                                参与者：{{item.creatorname}}
                                                            </span>

                                                            <span class="ecrw-exam-list-other-span">
                                                                浏览次数：<span class="ecrw-color-red">
                                                                    {{item.viewtimes}}
                                                                </span>
                                                            </span>
                                                            <span class="ecrw-exam-list-other-span">
                                                                下载次数：
                                                                <span class="ecrw-color-red">{{item.downtimes}}</span>
                                                            </span>
                                                        </li>
                                                    </ul>
                                                </div>
                                            </div>
                                            <div class="ecrw-exam-list-right ecrw-exam-list-right-zhengji-own">
                                                <ul>
                                                    <li class="ecrw-exam-list-right-li">
                                                        <span class="ecrw-color-red">{{ECRUtil.formatScore(item.score)}}积分</span>
                                                    </li>
                                                    <li class="ecrw-exam-list-right-li">
                                                        <a :href="'resource/' + item.contentid" class="ecrw-exam-list-right-down" target="_blank">查看资源</a>
                                                    </li>
                                                </ul>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <el-col :span="24" style="margin-bottom: 50px; margin-top: 30px;">
                                <my-pagination @current-change="handleCurrentChange" :page-size="page_size" :total="total"></my-pagination>
                            </el-col>
                        </div>
                    </div>
                </div>
                <div class="ecrw-zhengji-upload-right">
                    <div class="ecrw-zhengji-upload-right-wrap">
                        <div class="ecrw-zhengji-upload-user">
                            <div class="ecrw-zhengji-upload-user-left" v-if="userStatInfo!=null">
                                <ul>
                                    <li class="ecrw-zhengji-upload-user-left-pic">
                                        <a href="javascript:;">
                                            <img src="static/images/ecrw-head.png"/>
                                        </a>
                                    </li>
                                    <li class="ecrw-zhengji-upload-user-left-name">{{ loginInfo.name }}</li>
                                </ul>
                            </div>
                            <div class="ecrw-zhengji-upload-user-right" v-if="userStatInfo!= null">
                                <ul>
                                    <li class="ecrw-zhengji-upload-user-text" v-if="ECRUtil.isLoggedIn() && ECRUtil.isTeacher()">
                                        参与征集：<span class="ecrw-color-red">{{userStatInfo.collectionjoincount}}</span>次
                                    </li>
                                    
                                    <li class="ecrw-zhengji-upload-user-text" v-if="ECRUtil.isLoggedIn() && ECRUtil.isTeacher()">
                                        参与悬赏：<span class="ecrw-color-red">{{userStatInfo.rewardjoincount}}</span>次
                                    </li>
                                    <li class="ecrw-zhengji-upload-user-text" v-if="ECRUtil.isLoggedIn() && ECRUtil.isTeacher()">
                                        参与评比：<span class="ecrw-color-red">{{userStatInfo.estimatejoincount}}</span>次
                                    </li>
                                    <li class="ecrw-zhengji-upload-user-text" :class="{'upload-user-text': (ECRUtil.isLoggedIn() && ECRUtil.isStudent())}">
                                        我发起的悬赏：<span class="ecrw-color-red">{{userStatInfo.rewardcreatecount}}</span>次
                                    </li>
                                </ul>
                            </div>
                        </div>
                        <div class="ecrw-zhengji-upload-new">
                            <ecr-last-reward
                                ref="topreward"
                                >
                            </ecr-last-reward>
                        </div>
                        <div class="ecrw-zhengji-upload-new">
                            <ecr-top-rewarder
                                ref="toprewarder"
                                >
                            </ecr-top-rewarder>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>
<script>
    import ECRUtil from '../../common/js/util'
    import { CONTENT_TYPE } from '../../common/js/const.js';
    import { getManyRewardResourceTypeList, getActivityResourceList, getRewardCountInfo, getUserCountInfo } from '../../api/api'

    export default {
        data() {
            return {
                ECRUtil: ECRUtil,
                filters: {
                    activityname: '',
                    status: ''
                },
                loginInfo: {
                    name: '',
                    userid: '-1'
                },
                userStatInfo: null,
                page: 1,
                page_size: 5,
                total: 0,
                activity_goods: [],
                activity_goods_class:[],
                reward_countInfo: null,
                topReosurceType1: '',
                topReosurceType2: '',
                topContentType1: '',
                topContentType2: '',
                contentTypes: CONTENT_TYPE,
                activityid: '',
                thumbpath:{ localpath:'' },
                listLoading: false
            }
        },
        methods: {
            load() {
                this.getMyResourceList();
                this.getRewardCountInfo();
                this.getUserCountInfo();
            },
            selsChange: function (sels) {
                this.sels = sels;
            },
            handleCurrentChange(page) {
                this.page = page;
                this.load(this.type);
            },
            getTopCountType() {
                let params = {

                };
                getManyRewardResourceTypeList({}).then((res) => {
                    if (res.data.serverResult.resultCode == "200") {
                        this.topReosurceType1 = res.data.pageInfo.list[0];
                        this.topReosurceType2 = res.data.pageInfo.list[1];
                        this.getContentType();
                    } else {
                        this.$message({
                            message: res.data.serverResult.resultMessage,
                            type: 'error'
                        });
                        this.topReosurceType1 = [];
                        this.topReosurceType2 = [];
                    }
                });
            },
            getContentType() {
                for (var i = 0; i<this.contentTypes.length; i++) {
                    if (this.topReosurceType1 == this.contentTypes[i].contenttype) {
                        this.topContentType1 = this.contentTypes[i].contentname;
                    }
                    if (this.topReosurceType2 == this.contentTypes[i].contenttype) {
                        this.topContentType2 = this.contentTypes[i].contentname;
                    }
                }
            },
            getRewardCountInfo() {
                getRewardCountInfo({}).then((res) => {
                    if (res.data.serverResult.resultCode == "200") {
                        this.reward_countInfo = res.data.responseEntity;
                    } else {
                        this.$message({
                            message: res.data.serverResult.resultMessage,
                            type: 'error'
                        });
                    }
                });
            },
            getUserCountInfo() {
                getUserCountInfo(this.loginInfo.userid).then((res) => {
                    if (res.data.serverResult.resultCode == "200") {
                        this.userStatInfo = res.data.responseEntity;
                    } else {
                        this.$message({
                            message: res.data.serverResult.resultMessage,
                            type: 'error'
                        });
                    }
                });
            },  
            getMyResourceList() {
                let params = {
                    pagination:{
                        numPerPage:this.page_size,
                        pageNo:this.page,
                        calTotal:true
                    },
                    conditions: [
                        {
                            fieldName: 'activityid',
                            fieldValues: [
                                this.activityid
                            ],
                            prepender: null,
                            operator: 'EQUAL',
                            childCondtions: null
                        }
                    ]
                };
                this.listLoading = true;
                getActivityResourceList(params).then((res) => {
                    if (res.data.serverResult.resultCode == "200") {
                        this.total = res.data.pageInfo.total;
                        this.activity_goods = res.data.pageInfo.list;
                    } else {
                        this.$message({
                            message: res.data.serverResult.resultMessage,
                            type: 'error'
                        });
                        this.total = 0;
                        this.activity_goods = [];
                    }
                    this.listLoading = false;
                }).catch(() => {
                    this.listLoading = false;
                    this.total = 0;
                    this.activity_goods = [];
                    
                    this.$message({
                        message: '加载失败!',
                        type: 'error'
                    });
                });
            },
            goPage(url) {
                this.$router.push(url);
            }
        },
        created() {
            ECRUtil.authenticate(this.$router, '/home');
            
            var user = localStorage.getItem('frontend-user');
            if (user != null) {
                this.loginInfo.name = JSON.parse(user).realName;
                this.loginInfo.userid = JSON.parse(user).userId;
            }
            this.activityid = this.$route.params.id;
            this.load();          
        },
        watch: {
            "activity_goods": function (value) {
                for(var idx in value){
                    var thumbpath = {};
                    this.activity_goods_class[idx] = {
                        'name': '',
                        'path': ''
                    };                   
                    this.activity_goods_class[idx].name = ECRUtil.getDefaultThumb(value[idx], thumbpath);
                    this.activity_goods_class[idx].path = thumbpath;
                }                
            }
        },        
    }
</script>

<style scoped lang="scss">
    @import '~scss_vars';
    .upload-user-button {
        margin-top: 50px;
    }
    .upload-user-text {
        padding-top: 42px;
    }

</style>