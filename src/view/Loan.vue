<template>
  <div class="hello">
    <div>
      <mu-list>
        <mu-sub-header>最近聊天记录</mu-sub-header>
        <mu-list-item title="聊天室1" @click="chatwindow('room1')">
          <mu-avatar src="//s3.qiufengh.com/images/house.png" slot="leftAvatar"/>
          <mu-icon value="chat_bubble" slot="right"/>
        </mu-list-item>
        <mu-list-item title="聊天室2" @click="chatwindow('room2')">
          <mu-avatar src="//s3.qiufengh.com/images/house2.png" slot="leftAvatar"/>
          <mu-icon value="chat_bubble" slot="right"/>
        </mu-list-item>
      </mu-list>
      <mu-divider/>
      <mu-list>
        <mu-sub-header>历史聊天记录</mu-sub-header>
        <mu-list-item title="聊天室1" @click="chatHistory('room1')">
          <mu-avatar src="//s3.qiufengh.com/images/house.png" slot="leftAvatar"/>
          <mu-icon value="chat_bubble" slot="right"/>
        </mu-list-item>
        <mu-list-item title="聊天室2" @click="chatHistory('room2')">
          <mu-avatar src="//s3.qiufengh.com/images/house2.png" slot="leftAvatar"/>
          <mu-icon value="chat_bubble" slot="right"/>
        </mu-list-item>
      </mu-list>
      <mu-list>
        <mu-sub-header>和小白聊天</mu-sub-header>
        <mu-list-item title="聊天室1" @click="chatRobot()">
          <mu-avatar src="//s3.qiufengh.com/avatar/robots.jpg" slot="leftAvatar"/>
          <mu-icon value="chat_bubble" slot="right"/>
        </mu-list-item>
      </mu-list>
    </div>
  </div>
</template>

<script>
  import Confirm from '@components/Confirm';
  import {mapState} from 'vuex';

  export default {
    async mounted() {
      this.$store.commit('setTab', true);
    },
    methods: {
      chatHistory(roomID) {
        this.$store.commit('setTab', false);
        this.$router.push({path: '/chat-history', query: {roomId: roomID}});
      },
      async chatwindow(roomID) {
        const uerId = this.userid;
        if (!uerId) {
          const res = await Confirm({
            title: '提示',
            content: '聊天请先登录，但是你可以查看聊天记录哦~'
          });
          console.log(res);
          if (res === 'submit') {
            this.$router.push({ path: 'login' });
          }
          return;
        }
        this.$store.commit('setTab', false);
        this.$router.push({path: '/chat', query: {roomId: roomID}});
      },
      chatRobot() {
        this.$store.commit('setTab', false);
        this.$router.push({path: '/robot'});
      }
    },
    computed: {
      ...mapState({
        userid: state => state.userInfo.userid,
        src: state => state.userInfo.src
      })
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus" rel="stylesheet/stylus">

</style>
