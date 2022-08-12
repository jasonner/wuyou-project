<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
import wx from "weixin-js-sdk";
  export default {
    name: 'App',
    data(){
      return {
        
      }
    },
    mounted(){
      this.getKey();
      if(this.isWeixin()){ // 微信环境下禁用分享
        document.addEventListener('WeixinJSBridgeReady', function onBridgeReady() {
          // 通过下面这个API隐藏右上角按钮
          WeixinJSBridge.call('hideOptionMenu');
        });
      }
    },
    methods:{
      isWeixin() {
        var ua = navigator.userAgent.toLowerCase();
        if (ua.match(/MicroMessenger/i) == "micromessenger") {
            return true;
        } else {
            return false;
        }
      },

      getKey(){
        let that = this;
        let promise = {
          url:location.href.split('#')[0]
        }
        that.axios.get('https://api.lehuitech.com.cn/WXAuthorization/jsapi',promise)
        .then(function (res) {
            console.log(res);
            if(res.status == 200 ){
                that.initIMg(res.data);
            }else{
                that.$message({
                message: '服务异常请稍后重试！',
                type: 'warning'
                });
            }
        });
      },
      initIMg(result){
        console.log(result.appId);
        wx.config({
            debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
            appId: result.appId,// 必填，公众号的唯一标识
            timestamp: result.timestamp,  // 必填，生成签名的时间戳
            nonceStr: result.nonceStr, // 必填，生成签名的随机串
            signature: result.signature,// 必填，签名
            jsApiList:  ['chooseImage','previewImage'], // 必填，需要使用的JS接口列表
        });
        wx.ready(function () {
         
        })
        wx.error(function (res) {
          // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名
          console.log(res)
        });
      },
    },
  }
</script>

<style>
html,body{
  width: 100%;
  height: 100%;
}
#app {
  font-family:SourceHanSans, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #303030;
  width: 100%;
  height: 100%;
  background-size: 100% 100%;
  background: #fbfdfc;
  margin: 0;
  padding: 0;
}
body,h1,h2,h3,h4,h5,h6,div,dd,dt,dl,p,form,kbd,table,ul,li,marquee,hr,blockquote,ol,pre,fieldset,legend,button,input,textarea,th,td{font-size:12px; margin:0; padding:0; font-family:SourceHanSans, "微软雅黑",Arial, "宋体", Tahoma, Verdana,  Helvetica, sans-serif;}
address,cite,dfn,em,var{font-style:normal;}
button,input,select,textarea{font-size:100%; cursor:pointer}
input{ cursor:pointer}
table{border-collapse:collapse;border-spacing:0;}
ul,ol{list-style:none;}
img{border:0;width: 100%;object-fit: fill;}
ul{list-style:none;}
.clear{ clear:both;}
a{text-decoration: none; color: #666666; }
a:hover{ color:#000; text-decoration:none}
input[type="submit"],input[type="reset"],input[type="button"],input[type="text"], button { -webkit-appearance: none;border-radius: 0; } 

.clearfix:after { visibility: hidden; display: block; font-size: 0; content: " "; clear: both; height: 0; }
::-webkit-scrollbar {
  display: none; /* Chrome Safari */
  -ms-overflow-style: none; /* IE 10+ */
  scrollbar-width: none; /* Firefox */  
}
input::-webkit-input-placeholder { 
/* WebKit browsers */ 
  color: #adadad; 
} 
input:-moz-placeholder { 
/* Mozilla Firefox 4 to 18 */ 
  color: #adadad; 
} 
input::-moz-placeholder { 
/* Mozilla Firefox 19+ */ 
  color: #adadad; 
} 
input:-ms-input-placeholder { 
/* Internet Explorer 10+ */ 
  color: #adadad; 
}

</style>
<style>
.content-user-sex .van-radio__icon{
    font-size: 4vw;
    height: auto;
    line-height: 2;
}
.van-picker-select{
    width: 100%;
    height: 35vh;
    background: #fff;
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 5;
}
.van-picker-model{
    width:100vw;
    height: 100%;
    background: #000;
    opacity: 0.3;
    position: fixed;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    z-index: 1;
}
.van-picker-select .van-picker__cancel, .van-picker-select .van-picker__confirm{
    font-size: 4vw;
}
.van-picker-select .van-picker__confirm{
    color: #2fc0c5;
}
.van-picker-select .van-ellipsis{
    font-size: 4vw;
}
.van-picker-select  .van-picker__toolbar{
    height: 10vw;
}
.van-dialog{
    width: 70% !important;
}
.van-dialog__header{
    font-size: 4vw;
}
.van-dialog__message{
   line-height: 1.5 !important;
   font-size: 4vw !important;
   margin-top: 3vw;
}
.van-dialog__confirm, .van-dialog__confirm:active{
    color: #2FC0C5 !important;
    height: 6vh !important;
}
.van-dialog__footer{
    margin-top: 3vw;
}
.van-button__text{
     font-size: 4vw !important;
}
.van-radio__label{
    font-size: 28px;
}
</style>