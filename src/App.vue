<template>
  <div id="app">
    <div class="pop-l" :class="{'out': loading}"></div>
    <div class="pop-r" :class="{'out': loading}"></div>
    <div class="pop-pic" :class="{'out': loading,'transition-fast': loading,'transition-slow': !loading}"></div>
    <i class="refresh" title="刷新" @click="handleRefresh"></i>
    <div class="left-container">
      <div class="show-name">{{showName}}</div>
      <div class="btn">
        <span v-if="!btnFlag" :key="1" @click="handleStart">点击开始</span>
        <span v-else :key="2" @click="handleStop">停止</span>
      </div>
    </div>
    <div class="right-container">
      <ul class="show-list">
        <li v-for="(e, i) in showList" :key="i">{{i+1}}、{{e}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      loading: false,

      userList: [], //获取的人名
      btnFlag: false, //开始、停止状态：true开始，false停止

      showName: 'Are You Ready?', //选中的人名
      showList: [],
      num: null
    }
  },
  methods: {
    getUserList() {
      this.loading = false;
      window.setTimeout(() => {
        this.$axios.get('/api/userName').then(({data}) => {
          this.loading = true;
          this.userList = data.name;
        }).catch(() => {
          this.loading = true;
          alert('获取名单失败！');
        })
      },1500);
    },

    /**
     * 刷新
     * */
    handleRefresh() {
      this.getUserList();
      window.setTimeout(() => {
        this.btnFlag = false;
        this.showName = 'Are You Ready?';
        this.showList = [];
        this.num = null;
      },1500);
    },

    /**
     * 点击开始是执行计时器
     * btnFlag状态改为true计时器即可开始
     * */
    handleStart() {
      if (this.userList.length > 0) {
        this.btnFlag = true;
        let interval = window.setInterval(() => {
          if (this.btnFlag) {
            this.num = parseInt((this.userList.length)*Math.random());
            this.showName = this.userList[this.num];
          } else {
            window.clearInterval(interval);
          }
        },15);
      } else {
        alert('抽奖人数已达上限！请点击刷新。');
      }
    },

    /**
     * 点击开始是执行计时器
     * btnFlag状态改为false计时器即可停止
     * */
    handleStop() {
      this.btnFlag = false;
      this.showList.push(this.showName);
      this.userList.splice(this.num,1);
    },

  },
  mounted() {
    this.getUserList();
  }
}
</script>

<style>
  *{margin: 0;padding: 0;}
  html,body{height: 100%;color: #333;}
  #app{background: url("./assets/gif.gif");background-size:100%;width: 100%;height: 100%;}
  .pop-l{z-index: 101;position: fixed;width: 50%;height: 100%;left: 0;top: 0;background-color: #D1E3AB;transition: all 1.5s ;}
  .pop-l.out{left: -100%;}
  .pop-pic{z-index: 200;position: fixed;right: 50%;top: 50%;margin-top: -100px;margin-right:-100px;width: 200px;height: 200px;background: url("./assets/fengzheng.png");background-size:100%;}
  .pop-pic.transition-slow{transition: all 2s;}
  .pop-pic.transition-fast{transition: all 1s;}
  .pop-pic.out{top:-200px;}
  .pop-r{z-index: 100;position: fixed;width: 50%;height: 100%;right: 0;top: 0;background-color: #297689;transition: all 1.5s ;}
  .pop-r.out{right: -100%;}
  .refresh{z-index: 10;position: absolute;right: 70px;top: 70px;width: 50px;height: 50px;background: url("./assets/fengche.png") no-repeat;background-size: 50px;cursor: pointer;transition: all 2s;}
  .refresh:hover{transform: rotate(360deg);}
  .left-container{float: left;position: relative;width: 50%;height: 100%;}
  .left-container .show-name{position: absolute;right: 0;top: 25%;width: 600px;line-height: 90px;font-size: 70px;text-align: center;}
  .left-container .btn{position: absolute;right: 0;top: 55%;width: 600px;line-height: 90px;font-size: 60px;color: orange;text-align: center;}
  .left-container .btn span{cursor: pointer;user-select: none;}
  .right-container{float: left;position: relative;width: 50%;height: 100%;}
  .right-container .show-list{position: absolute;left: 10%;top: 25%;list-style: none;width: 250px;max-height: 315px;overflow: auto;}
  .right-container .show-list li{font-size: 26px;line-height: 35px;}

  /*—滚动条默认显示样式–*/
  ::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, .5);
    height: 50px;
  //outline-offset:-2px;
  //outline:2px solid #fff;
    -webkit-border-radius: 5px;
  //border: 2px solid #fff;
  }

  /*—鼠标点击滚动条显示样式–*/
  ::-webkit-scrollbar-thumb:hover {
  //height:50px;
  //-webkit-border-radius:5px;
  }

  /*—滚动条大小–*/
  ::-webkit-scrollbar {
    width: 8px;
    height: 10px;
  }

  /*—滚动框背景样式–*/
  ::-webkit-scrollbar-track-piece {
    background-color: #fff;
  //-webkit-border-radius:0;
  }

  .bdb{
    box-sizing:border-box;
  }
  /*-计算字符串占用像素-*/
  #word-cal{
    visibility: hidden;
    white-space: nowrap;
  }
</style>
