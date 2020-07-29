<template>
  <div class="container">
    <Bulletin />
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
                <router-link :to="{name: 'cheater', params: {game: `${activity.game}`, ouid: `${activity.originUserId}`}}">{{ activity.cheaterOriginId }}</router-link>
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
                <router-link :to="{name: 'cheater', params: {ouid: `${activity.originUserId}`}}">{{ activity.cheaterOriginId }}</router-link>
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

      <p>
        <a href="https://bfban.com">本站</a>可以 <b>永久追踪</b> 被举报者的游戏ID，并支持 <b>搜索历史ID</b> ！欢迎大家积极举报。
      </p>

      <p>如果遇到挂，可以</p>
      <p>
        1、先使用

        <a target="_blank" href="https://bf1.mygoare.com/">
          战地1外挂举报助手
        </a>

        给官方举报；
      </p>
      <p>
        2、自己在网站
        <router-link :to="{path: 'signup'}">注册</router-link>
        后，自己
        <router-link :to="{path: 'report'}">举报</router-link>
        ；
      </p>
      <p>
        3、举报后即会被纪录在案，即使修改了ID也能被追踪到；
      </p>

      <p>
        <!--<b>本站管理员：</b>-->
      </p>
      <p v-for="m in manager" :key="m.name">

        <a v-if="m.link" target="_blank" :href="m.link">{{ m.name }}</a>
        <span v-else>{{ m.name }}</span>
      </p>


<br>
      <h2>{{$t("home.howToUse.tools")}}</h2>
      <div class="tools">
        <p>
          <a target="_blank" href="https://battlefieldtracker.com/">
            battlefieldtracker.com
          </a>
          在线战绩查询网站，可以详细查询到各类数据
        </p>
        <p>
          <a target="_blank" href="https://bf1.mygoare.com/">
            【推荐】战地1外挂举报助手
          </a>
          在线生成举报文本
        </p>
        <p>
          <a target="_blank" href="https://www.247fairplay.com/">
            247fairplay
          </a>
          快速查询标记当前ID在BF3、BF4中的战绩进行异常标记，查询联ban史
        </p>
        <p>
          <a target="_blank" href="https://www.notion.so/1-5-f348c539fd4f41a08878f274a37c7d81">
            【推荐】战地PC/手机战绩速查工具
          </a>
          同时支持电脑手机快速查询战绩进行标记
        </p>
        <p>
          <a target="_blank" href="http://bfban.ys168.com/">
            战地周边工具
          </a>
          帮助你解决游戏平台问题的工具
        </p>
        <p>
          <a target="_blank" href="https://app.bfban.com/public/www/">
            【推荐】BFBAN APP
          </a>
          BFBAN手机客户端
        </p>
      </div>

<br>
      <h2>{{$t("home.howToUse.qqGroup")}}</h2>
      <div class="tools">
        <p>
          <a target="_blank" href="https://jq.qq.com/?_wv=1027&k=5PEqoDb">
            527565465
          </a>

          联ban局QQ群
        </p>
      </div>
    </div>
  </div>



</template>

<script>
  import Bulletin from '@/components/Bulletin.vue';
  import { getCheaterStatusLabel, getGameLabel } from '@/mixins/common';
  import ajax from '@/mixins/ajax';
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
  components: {
    Bulletin,
  },
  methods: {
    handleStatus: getCheaterStatusLabel,
    handleGameName: getGameLabel,
  },
  created() {
    ajax({
      method: 'get',
      url: '/activity',
    })
    .then(({data}) => {
      const d = data;
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

