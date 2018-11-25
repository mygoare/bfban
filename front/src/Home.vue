<template>
  <div class="container">
    <div class="content">
      <Divider>{{$t("home.activity.title")}}</Divider>

      <p>bfban.com  于 2018年11月4日 上线，收到 <b>{{ site.report }}</b> 次举报，已石锤了 <b>{{ site.cheater }}</b> 个挂壁，感谢大家的共同努力！</p>
      <table>
        <tbody>
          <tr v-for="activity in activities" :key="activity.id">
            <td nowrap>
              <Time v-if="activity.createDatetime" :time="activity.createDatetime"></Time>
            </td>
            <td v-if="activity.type === 'report'">
              <span>
                <router-link :to="{name: 'account', params: {uId: `${activity.uId}`}}">{{ activity.username }}</router-link>
                举报了
                <Tag>
                  {{ handleGameName(activity.game) }}
                </Tag>
                <router-link :to="{name: 'cheater', params: {game: `${activity.game}`, uid: `${activity.cheaterUId}`}}">{{ activity.cheaterOriginId }}</router-link>
              </span>

            </td>

            <td v-if="activity.type === 'register'">
              <span>
                <router-link :to="{name: 'account', params: {uId: `${activity.uId}`}}">{{ activity.username }}</router-link>
                注册了 bfban ，
                欢迎！
              </span>

            </td>

            <td v-if="activity.type === 'verify'">
              <span>
                <Tag color="success">管理员</Tag>
                <router-link :to="{name: 'account', params: {uId: `${activity.uId}`}}">{{ activity.username }}</router-link>
                将
                <Tag>
                  {{ handleGameName(activity.game) }}
                </Tag>
                <router-link :to="{name: 'cheater', params: {game: `${activity.game}`, uid: `${activity.cheaterUId}`}}">{{ activity.cheaterOriginId }}</router-link>
                处理为
                <Tag color="warning">
                  {{ handleStatus(activity.status) }}
                </Tag>
              </span>

            </td>
          </tr>
        </tbody>
      </table>

    </div>
    <div class="content">
      <Divider>{{$t("home.howToUse.title")}}</Divider>

      <p>如果遇到挂，可以</p>
      <p>
        1、先使用

        <a target="_blank" href="https://bf1.mygoare.com/">
          战地1外挂举报助手
        </a>

        给官方举报
      </p>
      <p>
        2、自己在网站
        <router-link :to="{path: 'signup'}">注册</router-link>
        后，自己
        <router-link :to="{path: 'report'}">举报</router-link>
        ；
        我们会有管理员定期审核。
      </p>
      <p>
        期间 你可以在 个人中心 查看进度状态， 也可以翻看
        <a href="/#/cheaters?status=1">外挂公示</a>
        。
      </p>

      <p>
        <!--<b>本站管理员：</b>-->
      </p>
      <p v-for="m in manager" :key="m.id">

        <a v-if="m.link" target="_blank" :href="m.link">{{ m.name }}</a>
        <span v-else>{{ m.name }}</span>
      </p>


      <h2>{{$t("home.howToUse.tools")}}</h2>
      <div class="tools">
        <p>
          <a target="_blank" href="https://battlefieldtracker.com/">
            在线战绩查询
          </a>
        </p>
        <p>
          <a target="_blank" href="https://bf1.mygoare.com/">
            战地1外挂举报助手
          </a>
        </p>
        <p>
          <a target="_blank" href="http://bf1report.oss-cn-beijing.aliyuncs.com/version/BF1%E5%BF%AB%E9%80%9F%E4%B8%BE%E6%8A%A5%E6%96%87%E6%9C%AC%E7%94%9F%E6%88%90.zip">
            战地1外挂举报助手本地版
          </a>
        </p>
      </div>

      <h2>{{$t("home.howToUse.qqGroup")}}</h2>
      <div class="tools">
        <p>
          <a target="_blank" href="https://jq.qq.com/?_wv=1027&k=5PEqoDb">
            联ban局投诉审查处QQ群 527565465
          </a>
        </p>
      </div>
    </div>
  </div>



</template>

<script>
  import { getCheaterStatusLabel, getGameLabel } from './common';
export default {
  data() {
    return {
      site: {},
      activities: [],
      manager: [
        {
          name: '',
          link: '',
          desc: '',
        }
      ]
    }
  },
  methods: {
    handleStatus: getCheaterStatusLabel,
    handleGameName: getGameLabel,
  },
  created() {
    axios({
      method: 'get',
      url: '/activity',
    })
    .then(res => {
      const d = res.data;
      if (d.error === 0) {
        let { registers, reports, verifies, number } = d.data;

        reports = _.each(reports, (v, k, l) => {
          v['type'] = 'report'
        });
        registers = _.each(registers, (v, k, l) => {
          v['type'] = 'register'
        });
        verifies = _.each(verifies, (v, k, l) => {
          v['type'] = 'verify'
        });

        let activities = [].concat(verifies, reports, registers);

        activities = _.sortBy(activities, (v) => {
          return (new Date(v.createDatetime)).getTime();
        }).reverse();

        this.activities = activities;

        this.site = number;
      }
    })
  }
}
</script>

