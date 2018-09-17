<template>
  <div class="user-avator-dropdown">
    <Dropdown @on-click="handleClick">
      <Avatar :src="userAvator"/>
      <Icon :size="18" type="md-arrow-dropdown"></Icon>
      <DropdownMenu slot="list">
        <DropdownItem name="username">{{nowuser}}</DropdownItem>
        <DropdownItem name="logout">退出登录</DropdownItem>
      </DropdownMenu>
    </Dropdown>
  </div>
</template>

<script>
import './user.less'
import axios from 'axios'
import { mapActions } from 'vuex'

export default {
  name: 'User',
  props: {
    userAvator: {
      type: String,
      default: ''
    }
  },
  methods: {
    ...mapActions([
      'handleLogOut'
    ]),
    handleClick (name) {
      switch (name) {
        case 'logout':
          this.handleLogOut().then(() => {
            axios.post('/api/logout').then(response => {
              if (response.data.code === 1) {
                this.$Message.success('您已成功退出登录，请稍候。')
                setTimeout(() => {
                  location.href = '/auth/login'
                }, 1500)
              } else {
                this.$Message.warning('会话状态操作失败，请尝试刷新页面。')
              }
            })
          })
          break
      }
    }
  },
  data () {
    return {
      nowuser: ''
    }
  },
  created () {
    axios.post('/api/currentUserInfo').then(response => {
      if (response.data.code !== 1) {
        location.href = '/auth/login'
      } else {
        this.nowuser = response.data.data.username
      }
    })
  }
}
</script>
