<template>
  <div class="container page">
    <van-nav-bar title="任务记录" class="nav-bar">
      <template #left>
        <van-icon name="arrow-left" color="#fff" @click="back()"/>
      </template>
    </van-nav-bar>
    <div class="main">
      <van-pull-refresh v-model="isLoading" @refresh="onRefresh">
        <van-empty v-if="list.length === 0" description="数据为空！" />
        <div v-else class="item_list" v-for="(v,key) in list" :key="key">
          <div class="lottery_info">
            <van-image class="cover" :src="v.ico">
              <template v-slot:loading>
                <van-loading type="spinner"/>
              </template>
            </van-image>
            <span class="period-number">{{v.expect}}</span>
            <span class="period-number">{{v.name}}</span>
          </div>
          <div class="recent">
            <div class="kuaisan-ball left">
              <van-image class="res-img" :src=" v.status === 0 ? 'img/lottery/open_num.gif' : 'img/lottery/shaizi/' + v.opencode[0] + '.png' " >
                <template v-slot:loading>
                  <van-loading type="spinner"/>
                </template>
              </van-image>
              <van-image class="res-img" :src=" v.status === 0 ? 'img/lottery/open_num.gif' : 'img/lottery/shaizi/' + v.opencode[1] + '.png' " >
                <template v-slot:loading>
                  <van-loading type="spinner"/>
                </template>
              </van-image>
              <van-image class="res-img" :src=" v.status === 0 ? 'img/lottery/open_num.gif' : 'img/lottery/shaizi/' + v.opencode[2] + '.png' " >
                <template v-slot:loading>
                  <van-loading type="spinner"/>
                </template>
              </van-image>
              <span class="res-des middle">{{v.status === 0 ? 0 : v.opencode[0] + v.opencode[1] + v.opencode[2]}}</span>
              <span class="res-des middle">{{v.status === 0 ? 0 : (v.opencode[0] + v.opencode[1] + v.opencode[2]) >= 11 && (v.opencode[0] + v.opencode[1] + v.opencode[2]) &lt;= 18 ? "大" : "小"}}</span>
              <span class="res-des middle">{{v.status === 0 ? 0 : (v.opencode[0] + v.opencode[1] + v.opencode[2]) % 2 === 0 ? "双" : "单"}}</span>
            </div>
          </div>
          <div class="topInfo">
            <span v-if="v.status === 1" style="color: #07c160">{{v.status_text}}</span>
            <span v-else>{{v.status_text}}</span>
            <span>下注金额：{{v.money}}</span>
          </div>
       <!--   <div class="topInfo">
            <span v-if="v.is_win === 1" style="color: #07c160">{{v.win_text}}</span>
            <span v-else >{{v.win_text}}</span>
            <span>金额：{{v.profit}}</span>
          </div> -->
          <!-- <div class="topInfo">
            <span>任务类型：</span>
            <span>{{v.type}}</span>
          </div> -->
          <div class="time">
            <span>下单时间：{{v.create_time}}</span>
          </div>
          <div class="time">
            <span>结算时间：{{v.update_time}}</span>
          </div>
        </div>
      </van-pull-refresh>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isLoading: false,
      list:[],
    };
  },
  methods: {
    back(){
      return window.history.back();
    },
    onRefresh() {
      setTimeout(() => {
        this.$toast('刷新成功');
        this.isLoading = false;
      }, 500);
    },
    getUserGameList(){
      this.$http({
        method: 'get',
        url: 'user_get_game_list'
      }).then(res=>{
        if(res.code === 200){
			console.log(res.data)
          this.list = res.data;
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
      this.getUserGameList();
    }
  }
};
</script>

<style lang='less' scoped>
@import "../../assets/css/base.css";
::v-deep .van-pull-refresh__track .van-pull-refresh__head *{
  color: #000000;
  font-size: 35px;
}

::v-deep .van-loading__text {
  color: #000000;
  font-size: 35px;
}
.container .main{
  position: relative;
  overflow: auto;
  background-color: #f2f2f5;
  height: 100%;
  padding: 0 10px;
}
.item_list{
  padding: 15px 15px;
  margin: 30px 10px;
  background: #fff;
  border-radius: 10px;
  line-height: 60px;
}

.item_list .topInfo span{
  flex: 1;
  font-size: 35px;
  font-weight: 700;
  color: #ff253f;
}
.item_list .time span{
  flex: 1;
  font-size: 25px;
  font-weight: 500;
  color: #000;
}

.item_list .topInfo span:last-child{
  float: right;
}
.item_list .desc span{
  font-size: 25px;
  font-weight: 700;
  color: #9b9b9b;
}
.item_list .cover{
  width: 60px;
  height: 60px;
  -o-object-fit: cover;
  object-fit: cover;
}
.item_list  .period-number{
  margin-left: 50px;
  margin-right: 10px;
  height: 50px;
  line-height: 60px;
  font-size: 35px;
  font-weight: 700;
  color: #000;
}
.item_list .lottery_info{
  display: flex;
}
.recent {
  display: flex;
  align-items: center;
  height: 100px;
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
</style>
