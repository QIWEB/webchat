<template>
  <div>
    <div class="container">
      <div class="title">
        <mu-appbar title="Title">
          <mu-icon-button icon="chevron_left" slot="left" @click="goback"/>
          <div class="center">
            聊天记录
          </div>
          <mu-icon-button icon="expand_more" slot="right" />
        </mu-appbar>
      </div>
      <div class="all-chat" v-show="isLoadingAchieve">
        <div style="height:70px"></div>
        <div class="chat">
          <div v-if="messageData.length === 0" class="chat-no-people">暂无消息,赶紧来占个沙发～</div>
          <div v-for="(obj,index) in messageData" :key="index">
            <Message
              :is-self="obj.username === userid" 
              :name="obj.username" 
              :head="obj.src" 
              :msg="obj.msg"
              :img="obj.img" 
              :mytime="obj.time"
            ></Message>
          </div>
          <template>
            <mu-pagination v-show="messageData.length > 10" :total="pages.total" :current="pages.current" :defaultPageSize="pages.defaultPageSize" @pageChange="handleClick">
            </mu-pagination>
          </template>
          <div style="height:20px"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6" scoped>
  import {mapGetters, mapState} from 'vuex';
  import {queryString} from '@utils/queryString';
  import Message from '@components/Message';
  import loading from '@components/loading/loading';

  export default{
    data() {
      return {
        roomid: '',
        messageData: [],
        isLoadingAchieve: false,
        pages: {
          total: 200,
          current: 1,
          defaultPageSize: 40
        }
      }
    },
    created() {
      const roomId = queryString(window.location.href, 'roomId')
      this.roomid = roomId
      if (!roomId) {
        this.$router.push({path: '/'})
      }
    },
    mounted() {
      this.handleClick()
    },
    methods: {
      async handleClick(newIndex) {
        loading.show()
        if (newIndex) {
          this.pages.current = newIndex
        }
        const data = {
          current: this.pages.current,
          roomid: this.roomid
        }
        const res = await this.$store.dispatch('getAllMessHistory', data)
        this.messageData = res.data
        this.pages.total = res.total
        loading.hide()
        this.isLoadingAchieve = true
      },
      goback () {
        this.$router.goBack()
        this.$store.commit('setTab', true)
      }
    },
    computed: {
      ...mapGetters([
        'getMessHistoryAll'
      ]),
      ...mapState({
        userid: state => state.userInfo.userid,
        src: state => state.userInfo.src
      })
    },
    components: {
      Message
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>

  .container
    width: 100%
    height: 100%
    overflow-y: scroll
    background: #ffffff
    -webkit-overflow-scrolling: touch
    .title
      position: fixed
      height: 50px
      top: 0
      width: 100%
      z-index: 1
      .center
        -webkit-box-flex: 1
        -webkit-flex: 1
        -ms-flex: 1
        flex: 1
        padding-left: 8px
        padding-right: 8px
        white-space: nowrap
        text-overflow: ellipsis
        overflow: hidden
        font-size: 20px
        font-weight: 400
        line-height: 56px
        text-align: center
    .chat
      .chat-no-people
        width: 100%
        height: 300px;
        line-height: 300px;
        text-align: center
        color: #D1CFD2
    .all-chat
      .online
        display: inline-block
        margin: 5px
        img
          width: 40px
          height: 40px
          border-radius: 100%
</style>
