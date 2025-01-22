<template>
  <div class="container page">
    <van-nav-bar :title="this.lottery.name" class="nav-bar">
      <template #left>
        <van-icon name="arrow-left" color="#fff" @click="back()"/>
      </template>
<!--      <template #right>-->
<!--        <div class="right">切换任务</div>-->
<!--      </template>-->
    </van-nav-bar>
    <div class="record">
        <div class="period">
            <van-image class="cover" :src="this.lottery.ico">
              <template v-slot:loading>
                <van-loading type="spinner"/>
              </template>
            </van-image>
            <span class="period-number">{{this.lottery.now_expect}}</span>
            <div class="next-number">
              <span>{{this.lottery.next_expect}}</span>
              <van-count-down :time="time" @finish="check()" />
            </div>

        </div>
        <div class="linear-gradient" style="background: linear-gradient(to right, rgba(126, 86, 120, 0), rgb(230, 195, 161), rgba(126, 86, 120, 0));"></div>
        <div class="recent">
          <div class="kuaisan-ball left">
            <van-image class="res-img" :src="this.shanzi_1">
              <template v-slot:loading>
                <van-loading type="spinner"/>
              </template>
            </van-image>
            <van-image class="res-img" :src="this.shanzi_2">
              <template v-slot:loading>
                <van-loading type="spinner"/>
              </template>
            </van-image>
            <van-image class="res-img" :src="this.shanzi_3">
              <template v-slot:loading>
                <van-loading type="spinner"/>
              </template>
            </van-image>
            <span class="res-des middle">{{this.sum}}</span>
            <span class="res-des middle">{{this.size}}</span>
            <span class="res-des middle">{{this.double}}</span>
          </div>
          <van-icon name="arrow-down" :class="{ up: active,down:!active }" @click="active ? active = false : active = true" />
        </div>
    </div>
    <div class="history_popup"></div>
    <div class="wrapper">
        <div class="options-bar">
            <div class="game">
              <div class="tips">
                <p class="odds">【{{this.lottery.desc}}】</p>
                <div class="play-tip" >
                  <van-icon name="more-o" />
<!--                  <span class="span-text" @click="playgame = true">玩法提示</span>-->
                  <span class="span-text" @click="$router.push({path:'/GameRecord'});">任务记录</span>
                  <van-popup  class="mask" get-container="body" v-model="playgame">
                      <div class="play-type-tip">
                        <div class="title">玩法规则</div>
                        <div class="wrapper">
                          <div class="item">
                              <van-icon name="info-o" />
                              <div class="content">
                                <p class="content-title">玩法提示</p>
                                <p class="content-detail">从可选和值形态里面选择号码进行下注</p>
                              </div>
                          </div>
                          <div class="item">
                            <van-icon name="comment-o" />
                            <div class="content">
                              <p class="content-title">中奖说明</p>
                              <p class="content-detail">三个开奖号码总和值11~18 为大;总和值3~ 10为小;</p>
                            </div>
                          </div>
                          <div class="item">
                            <van-icon name="description" />
                            <div class="content">
                              <p class="content-title">投注范例</p>
                              <p class="content-detail">投注方案：小 开奖号码：123,即中小</p>
                            </div>
                          </div>
                        </div>
                      </div>
                  </van-popup>
                </div>
              </div>
              <div class="linear-gradient" style="background: linear-gradient(to right, rgba(126, 86, 120, 0), rgb(230, 195, 161), rgba(126, 86, 120, 0));"></div>
              <div class="sumValueTwoSides">
                <div class="rectangle large" :class="{active:choose[v.type]}" v-for="(v,key) in lottery_peilv_list" :key="key" @click="choosePlay(v.type,v.name);">
                  <div class="wrapper">
                    <div class="content">
                      <p class="name-text large">{{v.name}}</p>
                      <p class="odd-text large">{{v.proportion}}</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
        </div>
        <div class="bottom-bar">
          <div class="bar">
            <div class="left">
              <div class="item" @click="shopping ? shopping = false : shopping = true ">
                <van-icon name="cart-o" class="jixuanico" />
                <span class="text">任务单</span>
              </div>
              <div class="line"></div>
            </div>
            <div class="mid">
              <span class="text">可用余额</span>
              <span class="text num">{{ this.userInfo.money }}</span>
              <span class="text">元</span>
            </div>
            <div class="right" @click="jiesuan()">
              提交
            </div>
          </div>
          <div class="wrapper" :class="{active:shopping}">
               <div class="item">
                 <span class="label">当前选号：</span>
                 <div class="bet-number">{{ this.shopchoose}}</div>
                 <van-icon name="arrow-down" :class="{ up: !shopping,down:shopping }" @click="shopping ? shopping = false : shopping = true" />
               </div>
              <div class="item">
                <span class="label">每注金额：</span>
                <div class="amount-wrapper">
                  <van-field v-model="money" type="digit" placeholder="请输入金额" />
                  <span class="label">元</span>
                </div>
              </div>
              <div class="item">
                <div class="part">
                  <span>总共</span>
                  <span class="number">{{this.formData.length}}</span>
                  <span>注</span>
                </div>
                <div class="part">
                  <span>合计</span>
                  <span class="number">{{this.formData.length * this.money}}</span>
                  <span>元</span>
                </div>

              </div>
          </div>
        </div>
      <van-popup v-model="jiesuanpage" get-container="body" >
        <div class="confirm-order-modal">
            <div class="head van-hairline--bottom">
              <p class="text">任务单</p>
            </div>
            <ui class="list">
                <li v-for="(v,key) in formData" :key="key" class="lise-item van-hairline--bottom">
                    <div class="main">
                      <p class="bet-name">{{ v.name }}</p>
                      <p class="detail-text">1注X{{ money }}元={{ money }}元</p>
                    </div>
                    <van-icon @click="clearChooes(v.type)" name="close" />
                </li>
            </ui>
            <div class="sub-bar">
              <van-button class="item cancel-btn" type="default" @click="allClear()">清空订单</van-button>
              <van-button class="item sub-btn" type="default" @click="doSub">确认提交</van-button>
            </div>
        </div>
      </van-popup>
      <van-popup v-model="active" position="top" :style="{ height: '70%' }" >
              <van-pull-refresh v-model="isLoading" @refresh="onRefresh">
                  <div class="wrapper">
                    <div class="item">
                      <div class="left font-weight">期号</div>
                      <div class="right font-weight" >开奖号码</div>
                    </div>
                    <div class="item" v-for="(v,key) in lottery_list" :key="key">
                      <div class="left font-weight">{{v.expect}}</div>
                      <div class="right font-weight" >
                        <div class="kuaisan-ball left">
                          <van-image class="res-img" :src="'img/lottery/shaizi/' + v.opencode[0] + '.png'">
                            <template v-slot:loading>
                              <van-loading type="spinner"/>
                            </template>
                          </van-image>
                          <van-image class="res-img" :src="'img/lottery/shaizi/' + v.opencode[1] + '.png'">
                            <template v-slot:loading>
                              <van-loading type="spinner"/>
                            </template>
                          </van-image>
                          <van-image class="res-img" :src="'img/lottery/shaizi/' + v.opencode[2] + '.png'">
                            <template v-slot:loading>
                              <van-loading type="spinner"/>
                            </template>
                          </van-image>
                          <span class="res-des middle">{{v.opencode[0] + v.opencode[1] + v.opencode[2]}}</span>
                          <span class="res-des middle">{{(v.opencode[0] + v.opencode[1] + v.opencode[2]) >= 11 && (v.opencode[0] + v.opencode[1] + v.opencode[2]) &lt;= 18 ? "大" : "小"}}</span>
                          <span class="res-des middle">{{(v.opencode[0] + v.opencode[1] + v.opencode[2]) % 2 === 0 ? "双" : "单"}}</span>
                        </div>
                      </div>
                    </div>
                  </div>
              </van-pull-refresh>
        </van-popup>
    </div>
  </div>
</template>

<script>
var time;
var count = 0;
export default {
  data() {
    return {
      jiesuanpage:false,
      choose: {
        "大":false,
        "小":false,
        "单":false,
        "双":false,
        "3":false,
        "4":false,
        "5":false,
        "6":false,
        "7":false,
        "8":false,
        "9":false,
        "10":false,
        "11":false,
        "12":false,
        "13":false,
        "14":false,
        "15":false,
        "16":false,
        "17":false,
        "18":false,
      },
      playgame:false,
      shopping:false,
      isLoading: false,
      play:{},
      lottery_peilv_list:{},
      lottery_list:{},
      active: false,
      userInfo:{},
      lottery:{},
      shanzi_1:"0",
      shanzi_2:"0",
      shanzi_3:"0",
      sum:0,
      size:"",
      double:"",
      time:0,
      shopchoose:"未选择",
      gameitem:"",
      formData:[],
      money:"",
    };
  },
  methods: {
    back(){
      return window.history.back();
    },
    doSub(){
      if(this.money === "0"){
        this.$toast("金额错误！");
        return false;
      }
      if(this.formData.length === 0){
        this.$toast("请选号！");
        return false;
      }else if(this.money === ""){
        this.$toast("请填写金额！");
        return false;
      } else {
        if(this.userInfo.money - (this.money * this.formData.length) < 0 ){
          this.$toast("余额不足，请联系客服充值！");
          return false;
        }else {
          this.$http({
            method: 'post',
            data:{
               item:this.gameitem,
               money:this.money,
               lid:this.lottery.id,
               mid:this.userInfo.id,
               expect:this.lottery.now_expect
            },
            url: 'game_place_order'
          }).then(res=>{
            if(res.code === 200){
              this.$toast(res.msg);
              this.allClear();
              this.getUserInfo();
            }else if(res.code === 401){
              this.$toast(res.msg);
            }
          })
          return true;
        }
      }
    },
    allClear(){
      for(var i = 0;i<this.formData.length;i++){
          this.choose[this.formData[i]['type']] = false;
      }
      this.formData.length = 0;
      this.money = "";
      this.shopchoose = "未选中";
      this.gameitem ="";
      this.shopping = false;
      this.jiesuanpage = false;
    },
    jiesuan(){
      if(this.formData.length === 0){
        this.$toast("请选号！");
        return false;
      }else if(this.money === ""){
        this.$toast("请填写金额！");
        return false;
      }
      else {
        this.jiesuanpage ? this.jiesuanpage = false : this.jiesuanpage = true;
      }

    },
    clearChooes(type){
      for(var i = 0;i<this.formData.length;i++){
        if(type === this.formData[i]['type'] ){
          this.formData.splice(i,1)
          this.choose[type] = false;
        }
      }
      if(this.formData.length >= 1){
        for(var j = 0;j < this.formData.length;j++){
          if(j === 0){
            this.shopchoose = this.formData[j]['name'];
            this.gameitem = this.formData[j]['type'];
          }else {
            this.shopchoose += ","+this.formData[j]['name'];
            this.gameitem   += "," + this.formData[j]['type'];
          }
        }
      }else {
        this.shopchoose = "未选中";
        this.gameitem = "";
        this.shopping = false;
      }
      if(this.formData.length === 0){
        this.jiesuanpage = false;
      }
    },
    choosePlay(type,name){
        if(this.choose[type] === true){
          this.choose[type] = false;
          for(var i = 0;i<this.formData.length;i++){
            if(type === this.formData[i]['type'] ){
                this.formData.splice(i,1)
            }
          }
        }else if(this.choose[type] === false) {
          this.formData.push({'name':name, 'type':type})
          this.choose[type] = true;
        }
        if(this.formData.length === 1){
          this.shopping = true;
        }
        if(this.formData.length >= 1){
          for(var j = 0;j < this.formData.length;j++){
            if(j === 0){
              this.shopchoose = this.formData[j]['name'];
              this.gameitem = this.formData[j]['type'];
            }else {
              this.shopchoose += ","+this.formData[j]['name'];
              this.gameitem += ","+this.formData[j]['type'];
            }
          }
        }else {
          this.shopchoose = "未选中";
          this.gameitem = "";
          this.shopping = false;
        }

    },
    check(){
      if(!localStorage.getItem('token')){
        this.$router.push({path:'/Login'})
      }else {
        time = window.setInterval(() => {
          setTimeout(()=>{
            this.getUserInfo();
            this.getLotteryInfo();
            this.getLotteryList();
            count++;
            if(count > 5){
              clearInterval(time);
              count = 0;
            }
          },0)
        }, 300)
      }
    },
    onRefresh() {
      setTimeout(() => {
        this.$toast("刷新成功");
        this.getLotteryList();
        this.isLoading = false;
      }, 200);
    },
    getUserInfo(){
      this.$http({
        method: 'get',
        url: 'user_info'
      }).then(res=>{
        if(res.code === 200){
          this.userInfo = res.data;
        }else if(res.code ===401){
          this.$toast(res.msg);
        }
      })
    },
    getLotteryPeilv(){
      this.$http({
        method: 'get',
        data:{id:this.$route.query.id},
        url: 'lottery_get_peilv'
      }).then(res=>{
        if(res.code === 200){
          this.lottery_peilv_list = res.data;
        }else if(res.code ===401){
          this.$toast(res.msg);
        }
      })
    },
    getLotteryList(){
      this.$http({
        method: 'get',
        data:{key:this.$route.query.key},
        url: 'lottery_get_one_list'
      }).then(res=>{
        if(res.code === 200){
          this.lottery_list = res.data;
          this.getLotteryPeilv();
        }else if(res.code ===401){
          this.$toast(res.msg);
        }
      })
    },
    getLotteryInfo(){
      this.$http({
        method: 'get',
        data:{key:this.$route.query.key},
        url: 'lottery_get_info'
      }).then(res=>{
        if(res.code === 200){
          if(parseFloat(this.userInfo.money) < parseFloat(res.data.condition)){
            this.$toast("请联系管理员领取该任务!");
            this.$router.push({path:'/Home'})
            return false;
          }
          this.lottery = res.data;
          this.time = res.data.second * 1000;

          if(this.time/1000 === 59){
            this.$toast("开奖成功，期号："+this.lottery.now_expect);
          }
          this.shanzi_1 = "img/lottery/shaizi/" + res.data?.opencode[0] + ".png";
          this.shanzi_2 = "img/lottery/shaizi/" + res.data?.opencode[1] + ".png";
          this.shanzi_3 = "img/lottery/shaizi/" + res.data?.opencode[2] + ".png";
          this.sum = res.data.opencode[0] + res.data.opencode[1] + res.data.opencode[2];
          if(this.sum >= 11 && this.sum <=18){
            this.size = "大";
          }else if(this.sum >= 3 && this.sum <= 10){
            this.size = "小";
          }
          if(this.sum % 2 === 0){
            this.double = "双";
          }else {
            this.double = "单";
          }
        }else if(res.code ===401){
          this.$toast(res.msg);
        }
      })

    }
  },
  created() {
    if(!localStorage.getItem('token')){
      this.$router.push({path:'/Login'})
    }else {
        this.getUserInfo();
        this.getLotteryInfo();
        this.getLotteryList();

    }
  },
  destroyed() {
    clearInterval(time);
  }
};
</script>

<style lang='less' scoped>
@import "../../assets/css/base.css";
.nav-bar .right{
  padding-left: 8px;
  padding-right: 8px;
  color: #fff;
  font-size: 28px;
  border-radius: 10px;
  border: 2px solid #fff;
  line-height: 60px;
}
.record{
  padding-left: 20px;
  padding-right: 20px;
  background-color: #fff;
  box-shadow: 0 2px 2px 0 #cacaca;
  z-index: 1;
}
.record .period{
  display: flex;
  align-items: center;
  padding: 20px 0;
}
.record .period .cover{
  width: 60px;
  height: 60px;
  -o-object-fit: cover;
  object-fit: cover;
}
.record .period .period-number{
  flex: 1;
  margin-left: 20px;
  margin-right: 10px;
  height: 50px;
  line-height: 50px;
  font-size: 35px;
  font-weight: 700;
  color: #000;
}
.van-count-down {
  color: #ff253f;
  font-size: 45px;
  margin-top: 10px;
  float: right;
}
.linear-gradient{
  width: 100%;
  height: 2px;
}
.record .recent{
  display: flex;
  align-items: center;
  height: 110px;
}
.kuaisan-ball .left{
  justify-content: flex-start;
}
.kuaisan-ball{
  flex: 1;
  display: flex;
  align-items: center;
}

.kuaisan-ball .res-img{
  width: 70px;
  height: 70px;
  margin-right: 30px;
}
.kuaisan-ball .res-des{
  font-weight: 700;
  text-align: center;
  color: #000;
}
.kuaisan-ball .res-des.middle{
  width: 15%;
  font-size: 35px;
}
.van-icon {
  font-size: 40px;
}
.down {
  transition: all .5s;
}
.up{
  transform: rotate(180deg);
  transition: all .5s;
}
.wrapper{
  position: relative;
  flex: 1;
  overflow: hidden;
}
.options-bar{
  display: flex;
  align-items: center;
  height: calc(100% - 80px);
}
.options-bar .game {
  flex: 1;
  height: 100%;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
.options-bar .game .tips{
  display: flex;
  align-items: center;
  height: 100px;
  padding: 0 20px;
}
.options-bar .game .tips .odds{
  flex: 1;
  font-size: 35px;
  font-weight: 500;
  color: #ff253f;
}
.options-bar .game .tips .play-tip{
  display: flex;
  align-items: center;
  height: 100%;
}
::v-deep .van-icon-more-o{
  color: #ff253f;
  font-size: 50px;
}
.options-bar .game .tips .play-tip .span-text{
  margin-left: 10px;
  font-size: 35px;
  font-weight: bolder;
  color: #ff253f;
}
.linear-gradient{
  width: 100%;
  height: 2px;
}
.sumValueTwoSides{
  display: flex;
  padding: 30px 0;
  flex-direction: row;
  align-items: center;
  justify-content: flex-start;
  flex-wrap: wrap;
}
.rectangle{
  overflow: hidden;
}
.rectangle.large{
  margin: 0 0 30px 4%;
  width: 20%;
  border-radius: 10px;
}
.rectangle .wrapper{
  position: relative;
  padding: 0 10px;
  background: #fff;
}
.rectangle .wrapper .content{
  position: absolute;
  display: flex;
  top: 0;
  left: 0;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}
.rectangle.large .wrapper{
  padding-bottom: 100%;
}
.rectangle .wrapper .content .name-text.large{
  font-size: 45px;
}
.rectangle .wrapper .content .name-text{
  color: #7d7c7c;
  font-weight: bolder;
}
.rectangle .wrapper .content .odd-text.large{
  font-size: 25px;
  margin-top: -30px;
}
.rectangle .wrapper .content .odd-text{
  text-align: center;
  color: #ff253f;
}
.bottom-bar {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  height: 100px;
  z-index: 2;
}
.bottom-bar .bar .left, .bottom-bar .bar{
  display: flex;
  flex-direction: row;
  align-items: center;
}
.bottom-bar .bar{
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 100px;
  background-color: #fff;
  box-shadow: 0 0 20px 0 #cacaca;
  z-index: 2;
}
.bottom-bar .bar .left, .bottom-bar .bar{
  display: flex;
  flex-direction: row;
  align-items: center;
}
.bottom-bar .bar .left .item{
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100px;
  font-size: 20px;
}
.bottom-bar .bar .left .item .text{
  font-size: 22px;
  color: #7d7c7c;
}
.jixuanico{
  font-size: 45px;
}
.bottom-bar .bar .left .line{
  width: 2px;
  height: 50px;
  background: #dadada;
}
.bottom-bar .bar .mid{
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
.bottom-bar .bar .mid .text{
  font-size: 30px;
  font-weight: 500;
  color: #000;
}
.bottom-bar .bar .mid .text.num{
  margin: 0 5px;
  color: #ff253f;
}
.bottom-bar .bar .right{
  padding: 0 30px;
  margin: 0 30px;
  color: #fff;
  background: linear-gradient(
      270deg,#e6c3a1,#7e5678);
  font-size: 40px;
  font-weight: 500;
  height: 70px;
  line-height: 70px;
  border-radius: 50px;
}
.rectangle.active .wrapper{
  background-color: #ff253f!important;
}

::v-deep .van-pull-refresh__track .van-pull-refresh__head *{
  color: #000000;
  font-size: 35px;
}
::v-deep   .van-popup {
  position: absolute;
}
::v-deep .van-overlay {
  position: absolute;
  background-color: rgb(70 67 67 / 70%);
}
::v-deep  .van-popup--top {
  top: -1px;
}
.wrapper .item{
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 10px 0;
}
.wrapper .item .left{
  width: 40%;
  font-size: 30px;
  text-align: center;
  font-weight: 500;
  color: #000;
}
.font-weight{
  font-weight: 700!important;
}
.wrapper .item .right{
  flex: 1;
  display: flex;
  font-size: 30px;
  justify-content: center;
  overflow: hidden;
  color: #000;
}
.wrapper .item .kuaisan-ball .left{
  justify-content: flex-start;
}
.wrapper .item .kuaisan-ball{
  margin-left: 20px;
  flex: 1;
  display: flex;
  align-items: center;
}
.wrapper .item .kuaisan-ball .res-img{
  width: 50px;
  height: 50px;
  margin-right: 20px;
}
.wrapper .item .kuaisan-ball .res-des{
  font-weight: 700;
  text-align: center;
  color: #000;
}
.wrapper .item .kuaisan-ball .res-des.middle{
  width: 15%;
  font-size: 35px;
}
.play-type-tip{
  position: unset;
  margin: auto;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 650px;
  height: 700px;
  max-height: 50%;
  z-index: 10;
  border-radius: 30px;
  overflow: hidden;
  background-color: #fff;
  color: #000;
}
.play-type-tip .title{
  line-height: 90px;
  background: linear-gradient(
      90deg,#7e5678,#e6c3a1);
  text-align: center;
  color: #fff;
  font-size: 35px;
  font-weight: 500;
}
.mask{
  background-color: rgb(0 0 0 / 0%);
  animation-duration: 0.35s;
}
.play-type-tip .wrapper{
  height: calc(100% - 10px);
  background-color: transparent;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
.play-type-tip .wrapper .item{
  padding: 40px 50px;
  display: flex;
  align-items: flex-start;
}
.play-type-tip .wrapper .item .van-icon{
  color: #e6c3a1;
  font-size: 60px;
}
.play-type-tip .wrapper .item .content .content-title{
  margin-top: 22px;
  font-size: 35px;
  font-weight: 500;
  color: #000;
  line-height: 0px;
}
.play-type-tip .wrapper .item .content .content-detail{
  margin-top: 5px;
  font-size: 22px;
  color: #000;
  line-height: 30px;
}
.play-type-tip .wrapper .item .content{
  flex: 1;
  margin-left: 30px;
}
.rectangle.active .wrapper{
  background-color: #ff253f!important;
}
.rectangle.active .wrapper .name-text, .rectangle.active .wrapper .odd-text{
  color: #fff!important;
}
.bottom-bar .wrapper{
  position: absolute;
  top: 10px;
  left: 0;
  right: 0;
  padding: 20px 20px 10px 20px;
  height: 230px;
  background-color: #fff;
  z-index: 1;
  box-shadow: 0 0 10px 0 #cacaca;
  transition: transform .3s cubic-bezier(.21,1.02,.55,1.01);
}
.bottom-bar .wrapper.active{
  transform: translateY(-100%);
}
.bottom-bar .wrapper .item{
  position: relative;
  display: flex;
  flex-direction: row;
  align-items: center;
  height: 65px;
}
.bottom-bar .wrapper .item .label{
  font-size: 30px;
  line-height: 30px;
  color: #000;
}
.bottom-bar .wrapper .item .bet-number{
  flex: 1;
  margin: 0 16px;
  overflow: auto;
  white-space: nowrap;
  -webkit-overflow-scrolling: touch;
  color: #ff253f;
  font-size: 30px;
  font-weight: 500;
  height: 40px;
  line-height: 40px;
}
.bottom-bar .wrapper .item .amount-wrapper{
  flex: 1;
  display: flex;
  flex-direction: row;
  align-items: center;
}
.van-cell {
  font-size: 30px;
  line-height: 50px;
}
.bottom-bar .wrapper .item .part{
  margin-right: 20px;
}
.bottom-bar .wrapper .item .part span{
  font-size: 30px;
  vertical-align: center;
  color: #000;
}
.bottom-bar .wrapper .item .part .number{
  margin: 0 5px;
  color: #ff253f;
  font-weight: 500;
}
::v-deep .van-field__control {
  color: #ff253f;
}
.confirm-order-modal{
  position: unset;
  display: flex;
  flex-direction: column;
  margin: auto;
  padding: 0 20px 30px;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 610px;
  height: 680px;
  max-height: 50%;
  z-index: 10;
  background-color: #fff;
  border-radius: 30px;
}
.confirm-order-modal .head{
  position: relative;
  height: 80px;
}
.confirm-order-modal .head .text{
  padding: 0 20px;
  height: 30px;
  line-height: 10px;
  text-align: center;
  font-size: 35px;
  font-weight: 500;
  color: #7e5678;
}
::v-deep .confirm-order-modal .van-hairline--bottom::after {
  border-bottom-width: 2px;
}
.van-popup--center {
  border-radius: 30px;
}
.confirm-order-modal .list{
  flex: 1;
  padding: 0 10px;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
.confirm-order-modal .list .lise-item{
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 10px 0;
}
.confirm-order-modal .list .lise-item .main{
  flex: 1;
  overflow: hidden;
}
.confirm-order-modal .list .lise-item .main .bet-name{
  color: #ff253f;
  font-size: 35px;
  font-weight: 500;
  line-height: 0px;
  word-wrap: break-word;
  word-break: break-all;
}
.confirm-order-modal .list .lise-item .main  .detail-text{
  line-height: 0px;
  font-size: 25px;
  color: #979799;
}
.confirm-order-modal .list .lise-item{
  color: #ff253f;
}
.confirm-order-modal .sub-bar{
  display: flex;
  align-items: center;
  margin-top: 30px;
  justify-content: space-around;
}
.confirm-order-modal .sub-bar .item{
  min-width: 40%;
  height: 80px;
  text-align: center;
  box-sizing: border-box;
  border-radius: 50px;
  font-size: 35px;
  font-weight: 500;
}
.confirm-order-modal .sub-bar .item.cancel-btn{
  border: 2px solid #979799;
  color: #979799;
  background-color: #fff;
}
.confirm-order-modal .sub-bar .item.sub-btn{
  background: linear-gradient(
      270deg,#e6c3a1,#7e5678);
  color: #fff;
  border: 0;
}
.next-number span{
  font-size: 35px;
  font-weight: 700;
  color: #000;
  float: right;
}
</style>
