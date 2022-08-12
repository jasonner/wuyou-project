<template>
    <div class="index-page">
        <div class="logo-box">
            <div class="logo-left">
                <img src="@/assets/img/index/logo-left.png" alt="" srcset="">
            </div>
            <div class="logo-right">
                <img src="@/assets/img/index/logo-right.png" alt="" srcset="">
            </div>
        </div>

        <!-- slogan -->
        <div class="slogan-box">
            <img src="@/assets/img/index/slogan.png" alt="" srcset="">
        </div>

        <div v-if="msgList.length<=0">
            <p class="noData-tip-box">{{noDataMsg}}</p>    
        </div>
        <div v-if="msgList.length>0">
            <div v-if="hadAnswered">
                <!-- list -->
                <div class="list-box">
                    <div class="list" v-for="(item,index) in msgList" :key="index">
                        <div class="title">{{index+1}}.{{item.title}}</div>
                        <div class="select-box" >
                            <div>
                                <span>A.{{item.o1}}</span>
                                <p>
                                    <img v-if="item.correctanswer  == 'A'" src="@/assets/img/index/Y.png" alt="" srcset="">
                                    <img v-if="item.correctanswer  == 'B'" src="@/assets/img/index/N.png" alt="" srcset="">
                                </p> 
                            </div>
                            <div>
                                <span>B.{{item.o2}}</span>
                                <p>
                                    <img v-if="item.correctanswer  == 'B'" src="@/assets/img/index/Y.png" alt="" srcset="">
                                    <img v-if="item.correctanswer  == 'A'" src="@/assets/img/index/N.png" alt="" srcset="">
                                </p> 
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div v-if="!hadAnswered">
                <!-- list -->
                <div class="list-box" v-if="!hasAnswered">
                    <div class="list" v-for="(item,index) in msgList" :key="index">
                        <div class="title">{{index+1}}.{{item.title}}</div>
                        <div class="select-box" >
                            <div @click="selectChange(item,index,'A')">
                                <span>A.{{item.o1}}</span>
                                <p v-if="questions1[index].seleced == 'A'" style="background:#1c4b5e"></p> 
                                <p v-else></p> 
                            </div>
                            <div @click="selectChange(item,index,'B')">
                                <span>B.{{item.o2}}</span>
                                <p v-if="questions1[index].seleced == 'B'" style="background:#1c4b5e"></p>
                                <p v-else></p>  
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 已经答过题 -->
                <div class="list-box" v-if="hasAnswered">
                    <div class="list" v-for="(item,index) in msgList" :key="index">
                        <div class="title">{{index+1}}.{{item.title}}</div>
                        <div class="select-box" >
                            <div>
                                <span>A.{{item.o1}}</span>
                                <p v-if="questions1[index].seleced == 'A'">
                                    <img v-if="item.correctanswer  == 'A'" src="@/assets/img/index/Y.png" alt="" srcset="">
                                    <img v-if="item.correctanswer  == 'B'" src="@/assets/img/index/N.png" alt="" srcset="">
                                </p> 
                                <p v-else>

                                </p>
                            </div>
                            <div>
                                <span>B.{{item.o2}}</span>
                                <p v-if="questions1[index].seleced == 'B'">
                                    <img v-if="item.correctanswer  == 'B'" src="@/assets/img/index/Y.png" alt="" srcset="">
                                    <img v-if="item.correctanswer  == 'A'" src="@/assets/img/index/N.png" alt="" srcset="">
                                </p> 
                                <p v-else>

                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>    
        </div>
        
        <!-- userMsg -->
        <div class="userMsg-box" v-if="msgList.length>0">
            <div class="title">
                <span></span>挑战者信息<span></span>
            </div>
            <div class="userMsg-list">
                <p class="name">姓名:</p>
                <p><input v-model="user.realName" type="text" name="" id=""></p>
            </div>
            <div class="userMsg-list">
                <p class="name">部门:</p>
                <p><input v-model="user.department" type="text" name="" id=""></p>
            </div>
            <div class="userMsg-list">
                <p class="name">手机:</p>
                <p><input v-model="user.phone" type="tel" maxlength="11" placeholder="" name="" id=""></p>
            </div>
            <div class="submit-box"  v-if="!hadAnswered">
                <img @click="submitChange()" src="@/assets/img/index/submitBTN.png" alt="" srcset="">
            </div>
        </div>
        <div class="tip-box">
            <p>仅内部使用   C-ANPROM/CN/PANTL/0003</p>
            <p>获批日期：2022年5月</p>
        </div>
    </div>
</template>
<script>
import Vue from 'vue'
import { Dialog } from 'vant';
Vue.use(Dialog);
export default {
    data(){
        return{
            openid:null,
            hadAnswered:false,//今日没有打过题
            hasAnswered:false,
            selecedALL:false,
            msgList:[],
            questions1:[{
                qId:'',
                answer:'',
                seleced:''
            },{
                qId:'',
                answer:'',
                seleced:''
            },{
                qId:'',
                answer:'',
                seleced:''
            },{
                qId:'',
                answer:'',
                seleced:''
            },{
                qId:'',
                answer:'',
                seleced:''
            }],
            user:{
                realName:'',
                department:'',
                phone:''
            },
            noDataMsg:''
        }
    },
    mounted(){
        let openid = this.$cookies.get("wuyouzhishi0509_openid");
        console.log(openid);
        if(openid){
            this.openid = openid;
            this.getQuestion();
        }else{
           window.location.replace('https://h5.lehuitech.com.cn/qr/qrred?k=7S04BBS1');
        }
    },
    methods:{
        getQuestion(){
            let that = this;
            that.axios.get('/api/Short/GetQuestionByDate',{
                headers: {
                    openid:that.openid,
                }
            }).then(function (res) {
                console.log(res.data);
                if(res.data.s){
                    if(res.data.data && res.data.data !=''){
                        that.msgList =  res.data.data;
                        that.user.realName = res.data.data[0].name;
                        that.user.department = res.data.data[0].deparment;
                        that.user.phone = res.data.data[0].phone;
                        var hadAnswered = res.data.data.find(x => x.answer == '');
                        if(hadAnswered){
                            that.hadAnswered = false;
                        }else{
                            that.hadAnswered = true;
                        }
                    }else{
                        that.msgList = [];
                    }
                }else if(!res.data.data || res.data.data =='' || JSON.stringify(res.data.data) == '{}'){
                    that.noDataMsg = res.data.msg;
                }
            })
        },
        selectChange(item,index,type){
            this.questions1[index].seleced = type;
            this.questions1[index].qId = item.id;
            if(type =='A'){
                this.questions1[index].answer = item.o1;
            }else{
                this.questions1[index].answer = item.o2;
            }
            console.log(this.questions1);
        },

        submitChange(){
            let that = this;
            var obj = {};
            var questions =[];
            var selecedALL = that.questions1.find(x => x.seleced == '');
            if(selecedALL){
                that.selecedALL = true;
            }else{
                that.selecedALL = false;
            }
            that.questions1.forEach(element => {
                obj ={
                    qId: element.qId,
                    answer:element.answer,
                }
                questions.push(obj);
            });
            if(that.selecedALL){
                Dialog.alert({
                    title: '提示',
                    message:'请答完所有选项！',
                }).then(() => {
                    // on close
                    that.hasAnswered = false;
                });
            }else{
                let promise = {
                    questions:questions,
                    realName:that.user.realName,
                    department:that.user.department,
                    phone:that.user.phone,
                }
                console.log(promise);
                that.axios.post('/api/Short/PostASQ', {
                    questions:questions,
                    realName:that.user.realName,
                    department:that.user.department,
                    phone:that.user.phone,
                },
                    {headers:{ //头部参数
                        openid:that.openid
                    }
                })
                .then(function (res) {
                    console.log(res);
                    if(res.status && res.status == 200){
                        console.log(res.data)
                        if(res.data.s){
                        that.hasAnswered = true;
                            Dialog.alert({
                                title: '提示',
                                message: res.data.msg,
                            }).then(() => {
                                // on close
                            });
                        }else{
                            Dialog.alert({
                                title: '提示',
                                message: res.data.msg,
                            }).then(() => {
                                // on close
                                that.hasAnswered = true;
                            });
                        }
                    }else{
                        Dialog.alert({
                            title: '提示',
                            message: res.data.msg,
                        }).then(() => {
                            // on close
                            that.hasAnswered = true;
                        });
                    }
                });
            }
        }
    }
}
</script>
<style scoped>
    .index-page{
        width: 100%;
        min-height: 100%;
        background: url('../assets/img/index/bg.png') center no-repeat;
        background-size: 100% 100%;
    }
    .logo-box{
        width: 100%;
        display: grid;
        grid-template-columns: 50% 50%;
        text-align: left;
    }
    .logo-box .logo-left{
        text-align: left;
    }
    .logo-box .logo-left img{
       width: 94px;
       margin-top: 30px;
       margin-left: 26px;
    }
    .logo-box .logo-right{
        text-align: right;
    }
    .logo-box .logo-right img{
      width: 130px;
      margin-right: 32px;
      margin-top: 31px;
    }

    /* slogan */
    .slogan-box{
        width: 100%;
        margin-top: 47px;
    }
    /* list */
    .list-box{
        width: 72%;
        margin: 0 auto;
        text-align: left;
        margin-top: 80px;
        border-top: 3px solid #003b7e;
        padding-top: 30px;
        box-sizing: border-box;
    }
    .list-box .list{
        border-bottom: 1px dashed #003b7e;
        padding-bottom: 30px;
        box-sizing: border-box;
    }
    .list-box .list:nth-last-of-type(1){
        border-bottom:none;
    }
    .list-box .list .title{
        color: #003b7e;
        font-size: 24px;
        font-weight: 600;
        font-family: SourceHanSans;
        line-height: 48px;
    }
    .list-box .list .select-box{
        width: 100%;
        display: block;
        color: #003b7e;
        font-size: 24px;
        font-weight: 600;
        margin-top: 30px;
    }
    .list-box .list .select-box div{
        height: 6vw;
    }
    .list-box .list .select-box p {
        float: right !important;
        margin-right: 6vw;
        position: relative;
    }
    .list-box .list .select-box p img{
        width: 150%; 
       position: absolute;
       left: -1vw;
       top: -1vw;
    }
    .list-box .list .select-box span{
        float:left; 
        color: #003b7e;
        font-size: 24px;
    }
    .list-box .list .select-box p{
        width: 24px;
        height: 24px;
        border: 1px solid #003b7e;
        float:left;
        margin-left: 22px;
        margin-top: 2px;
    }
    /*userMsg*/
    .userMsg-box{
        width: 72%;
        margin: 0 auto;
        margin-top: 80px;
        padding-bottom: 320px;
        box-sizing: border-box;
       
    }
    .userMsg-box .title{
        line-height: 1.5;
        font-size: 34px;
        color: #003b7e;
        font-weight: 700;
    }
    .userMsg-box .title span{
        width: 32%;
        height: 2px;
        background: #003b7e;
        display: inline-block;
    }
    .userMsg-box .title span:nth-of-type(1){
        margin-right: 10px;
    }
    .userMsg-box .title span:nth-of-type(2){
        margin-left: 10px;
    }
    .userMsg-list{
        width: 100%;
        display: grid;
        grid-template-columns: 30% 70%;
        margin-top: 56px;
    }
    .userMsg-list p{
        font-size: 34px;
        color: #003b7e;
        line-height: 54px;
        font-weight: 600;
    }
    .userMsg-list input{
        width: 338px;
        height: 54px;
        padding: 0 20px;
        box-sizing: border-box;
        border-radius: 5px;
        border: 2px solid #003b7e;
        outline: #003b7e;
        font-size: 28px;
        font-weight: 600;
        color: #003b7e;
    }
    
    .submit-box{
        width: 100%;
        text-align: center;
        margin-top: 56px;
    }
    .submit-box img{
        width: 150px;
    }
    .tip-box{
        width: 100%;
        text-align: left;
        padding-left: 3vw;
        padding-bottom: 3vw;
        box-sizing: border-box;
    }
    .tip-box p{
        color: #fff;
        font-size: 20px;
        line-height: 1.5;
    }
    .noData-tip-box{
        color: #003b7e;
        font-size: 42px;
        font-weight: 700;
        margin-top: 24vw;
    }
</style>