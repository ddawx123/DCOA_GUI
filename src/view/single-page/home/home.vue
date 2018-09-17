<template>
  <div>
    <Row :gutter="20" style="margin-top: 20px;">
    <i-col span="10">
    <Card shadow>
      <p slot="title">
        <Icon type="md-person" />
        用户信息
        </p>
        <ul>
            <p>账号名：{{user_name}}</p>
            <p>上次访问IP：{{last_ip}}</p>
            <p>总在职天数：{{reg_days}}天</p>
        </ul>
    </Card>
    </i-col>
    <i-col span="14">
    <Card shadow>
      <p slot="title">
        <Icon type="ios-people" />
        积分信息
        </p>
        <ul>
            <p>可用积分：{{m_score}}</p>
            <p>当前用户组：{{user_group}}</p>
            <p>待办事项：{{things_num}}项</p>
        </ul>
    </Card>
    </i-col>
    </Row>
    <Row :gutter="20" style="margin-top: 20px;">
      <i-col span="10">
        <Card shadow>
          <chart-pie style="height: 300px;" :value="pieData" text="业务访问情况"></chart-pie>
        </Card>
      </i-col>
      <i-col span="14">
        <Card shadow>
          <chart-bar v-if="vable" style="height: 300px;" :value="barData" text="系统访问周活跃度分布"/>
        </Card>
      </i-col>
    </Row>
  </div>
</template>

<script>
import { ChartPie, ChartBar } from '_c/charts'
import axios from 'axios'
export default {
  name: 'home',
  components: {
    ChartPie,
    ChartBar
  },
  data () {
    return {
      user_name: '',
      last_ip: '',
      reg_days: '',
      m_score: '',
      user_group: '',
      things_num: '',
      vable: false,
      pieData: [
        {value: 1, name: '自动化办公'},
        {value: 2, name: '项目审批'},
        {value: 5, name: '资源共享'}
      ],
      barData: {
        Mon: 0,
        Tue: 0,
        Wed: 0,
        Thu: 0,
        Fri: 0,
        Sat: 0,
        Sun: 0
      }
    }
  },
  mounted () {
    let v = this
    axios.post('/api/currentUserInfo').then(response => {
      if (response.data.code !== 1) {
        location.href = '/auth/login'
      } else {
        this.user_name = response.data.data.username
        this.last_ip = response.data.data.lastip
        this.reg_days = parseInt((new Date().getTime() - new Date(response.data.data.reg_time.replace(/-/g, '/')).getTime()) / (1000 * 60 * 60 * 24))
        this.m_score = response.data.data.score
        switch (response.data.data.group) {
          case 'stdusr':
            this.user_group = '正式用户组'
            break
          case 'master':
            this.user_group = '特权用户组'
            break
          default:
            this.user_group = '临时用户组'
            break
        }
        this.things_num = response.data.data.wknum
        v.barData.Sun = JSON.parse(response.data.data.weeklog)[0]
        v.barData.Mon = JSON.parse(response.data.data.weeklog)[1]
        v.barData.Tue = JSON.parse(response.data.data.weeklog)[2]
        v.barData.Wed = JSON.parse(response.data.data.weeklog)[3]
        v.barData.Thu = JSON.parse(response.data.data.weeklog)[4]
        v.barData.Fri = JSON.parse(response.data.data.weeklog)[5]
        v.barData.Sat = JSON.parse(response.data.data.weeklog)[7]
        this.vable = true
      }
    }).catch(() => {
      this.$Message.error('网络连接超时，请稍候重试')
    })
  },
  created () {
    //
  }
}
</script>

<style lang="less">
.count-style{
  font-size: 50px;
}
</style>
