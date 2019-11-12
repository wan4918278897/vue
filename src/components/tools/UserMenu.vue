<template>
  <div class="user-wrapper" :class="theme">
    <!-- <span class="action">
      <a class="logout_title" target="_blank" href="http://jeecg-boot.mydoc.io">
        <a-icon type="question-circle-o"></a-icon>
      </a>
    </span> -->
    <header-notice class="action"/>
    <a-dropdown>
      <span class="action action-full ant-dropdown-link user-dropdown-menu">
        <a-avatar class="avatar" size="small" :src="getAvatar()"/>
        <span v-if="isDesktop()">欢迎您，{{ nickname() }}</span>
      </span>
      <a-menu slot="overlay" class="user-dropdown-menu-wrapper">
        <a-menu-item key="0">
          <router-link :to="{ name: 'account-center' }">
            <a-icon type="user"/>
            <span>个人中心</span>
          </router-link>
        </a-menu-item>
        <a-menu-item key="1">
          <router-link :to="{ name: 'account-settings-base' }">
            <a-icon type="setting"/>
            <span>账户设置</span>
          </router-link>
        </a-menu-item>
        <a-menu-item key="2" @click="updatePassword">
          <a-icon type="setting"/>
          <span>密码修改</span>
        </a-menu-item>
      </a-menu>
    </a-dropdown>
    <span class="action">
      <a class="logout_title" href="javascript:;" @click="handleLogout">
        <a-icon type="logout"/>
        <span v-if="isDesktop()">&nbsp;退出登录</span>
      </a>
    </span>
    <user-password ref="userPassword"></user-password>
  </div>
</template>

<script>
  import HeaderNotice from './HeaderNotice'
  import UserPassword from './UserPassword'
  import {mapActions, mapGetters} from 'vuex'
  import {mixinDevice} from '@/utils/mixin.js'
  import {notification, Icon} from 'ant-design-vue'

  export default {
    name: "UserMenu",
    mixins: [mixinDevice],
    components: {
      HeaderNotice,
      UserPassword
    },
    data() {
      return {
        shopId: '',
        url: {
          list: "/webSocket/yzx/sendOneWebSocket",
        }
      }
    },
    props: {
      theme: {
        type: String,
        required: false,
        default: 'dark'
      },

    },
    created() { // 页面创建生命周期函数

      this.initWebSocket()
    },
    destroyed: function () { // 离开页面生命周期函数
      this.webSocket0neClose();
    },
    methods: {
      ...mapActions(["Logout"]),
      ...mapGetters(["nickname", "avatar", "userInfo"]),
      getAvatar() {
        console.log('url = ' + window._CONFIG['imgDomainURL'] + "/" + this.avatar())
        return window._CONFIG['imgDomainURL'] + "/" + this.avatar()
      },
      handleLogout() {
        const that = this
        this.$confirm({
          title: '提示',
          content: '真的要注销登录吗 ?',
          onOk() {
            return that.Logout({}).then(() => {
              window.location.href = "/";
              //window.location.reload()
            }).catch(err => {
              that.$message.error({
                title: '错误',
                description: err.message
              })
            })
          },
          onCancel() {
          },
        });
      },
      updatePassword() {
        let username = this.userInfo().username
        this.$refs.userPassword.show(username)
      },

      collapse: function () {
        this.isCollapse = !this.isCollapse;
        if (this.isCollapse) {
          this.iconClass = "cebianlanzhankai";
        } else {
          this.iconClass = "cebianlanshouhui";
        }
      },
      initWebSocket: function () {
        // WebSocket与普通的请求所用协议有所不同，ws等同于http，wss等同于https ws://localhost:81/jeecg-boot/websocket/
        this.websock = new WebSocket("ws://"+window._CONFIG['webSocket']+ this.userInfo().id);
        this.websock.onopen = this.webSocketOneOpen;
        this.websock.onerror = this.webSocket0neError;
        this.websock.onmessage = this.webSocket0neMessage;
        this.websock.onclose = this.webSocket0neClose;
      },
      webSocketOneOpen: function () {
        console.log("WebSocket连接成功");
      },
      webSocket0neError: function (e) {
        console.log("WebSocket连接发生错误");
      },
      webSocket0neMessage: function (e) {
        let that = this
        let message=e.data;
        notification.open({
          message: '系统提示',
          description: message,
          duration: 3,
          placement: "bottomRight",
          icon: (h) => {
            return h('a-icon', {
              props: {
                type: 'mail',
                style: 'color: #108ee9',
                theme: "twoTone"
              },
            })
          },
          onClick: function () {
            that.$router.push({
              path: '/isps/userAnnouncement',
              name: 'isps-userAnnouncement'
            });
          }
        })
        // notification.open({
        //   message: '系统提示',
        //   description: '拒绝访问',
        //   duration: 4,
        //   icon:'',<Icon type="message" theme="twoTone" />
        //   placement:"bottomRight",
        //   onClick:function () {
        //     that.$router.push({
        //       path: '/isps/userAnnouncement',
        //       name: 'isps-userAnnouncement'
        //     });
        //   }
        // })
      },
      webSocket0neClose: function (event) {
        console.log('连接关闭');
        localStorage.setItem('isOk', '0');

        localStorage.setItem('tipsData', '');
      }
    }
  }
</script>

<style scoped>
  .logout_title {
    color: inherit;
    text-decoration: none;
  }
</style>