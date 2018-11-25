<template>
  <div class="container">
    <div class="content">
      <p class="hint">我们还没有 消息系统，但可以在下方举报的状态 来得知进度</p>
      <p class="hint">所有举报都可以 回复参与讨论</p>
      <p class="hint">若要补充证据，可以重复举报同一ID</p>

      <div v-if="account">
        <Divider>用户中心</Divider>
        <h2>个人信息</h2>
        <p>
          用户名：
          {{account.username}}
        </p>
        <p>
          身份：
          <Tag v-if="account.privilege === 'admin'" color="success">
            管理员
          </Tag>
          <Tag v-if="account.privilege === 'normal'">
            普通
          </Tag>
        </p>
        <p>
          加入日期：
          <Tag color="primary">
            <Time v-if="account.createDatetime" :time="account.createDatetime" />
          </Tag>
        </p>

        <br>
        <h2>个人举报</h2>
        <p v-if="account.reports.length === 0">
          还没有任何举报
        </p>
        <table>
          <tbody>
          <tr v-for="report in account.reports" :key="report.id">
            <td>
            <span>
          <Tag color="primary">
            <Time v-if="report.createDatetime" :time="report.createDatetime" />
          </Tag>
        </span>
            </td>
            <td>
        <span>
          举报了
          <router-link :to="{name: 'cheater', params: { game: `${report.game}`, uid: `${report.uId}`}}">{{report.originId}}</router-link>
        </span>
            </td>
            <td>
        <span>
          状态
          <Tag color="error">
            {{ handleStatus(report.status) }}
          </Tag>
        </span>
            </td>
            <td>
        <span>
          最近更新
          <Tag color="warning">
            <Time v-if="report.updateDatetime" :time="report.updateDatetime" />
            <span v-else>无</span>
          </Tag>
        </span>
            </td>
          </tr>
          </tbody>

        </table>
      </div>
    </div>
  </div>

</template>

<script>
  import { getCheaterStatusLabel } from './common';

  export default {
    data() {
      return {
        account: {
          username: '',
          originId: '',
          privilege: '',
          createDatetime: '',

          reports: [],
        }
      }
    },
    watch: {
      '$route': 'loadData',
    },
    created() {
      this.loadData();
    },
    methods: {
      loadData() {
        const { uId } = this.$route.params;
        axios({
          method: 'get',
          url: `/account/${uId}`,
        })
        .then((res) => {
          const d = res.data;

          if (d.error === 0) {
            this.account = d.data;
            let { reports } = d.data;
            this.account.reports = reports;
          } else {
            this.account = '';
            this.$Message.warning(d.msg);
          }
        });
      },
      handleStatus: getCheaterStatusLabel,
    }
  }
</script>

<style lang="scss">
  table {
    width: 100%;
    border-collapse: collapse;
    border-spacing: 0;

    td {
      border: 1px solid #dbdbdb;
      border-width: 0 0 1px;
      padding: .5em .75em;
      vertical-align: middle;
    }

    tbody tr:hover {
      background-color: #f3f3f3bd;
    }
  }
</style>
