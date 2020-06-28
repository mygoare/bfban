<template>
  <div class="container">
    <div class="content">
      <div v-if="isCheaterExist" style="position: relative">
        <div style="display: flex; flex-direction: column; position: relative;">

          <span style="font-size: 1.6rem;">
            {{ cheater.originId }}
          </span>

          <div>
            <Tag color="error">
              {{ handleStatus(cheater.status) }}
            </Tag>

            <Tag v-if="cheater.cheatMethods" color="warning">
              {{ convertCheatMethods(cheater.cheatMethods) }}
            </Tag>
          </div>
          <p>
            <span>
              围观
              <span class="is-size-6">{{ cheater.n || 0 }}</span>
              次
            </span>
            <span>
                回复
                <span class="is-size-6">{{ cheater.commentsNum || 0 }}</span>
                次
            </span>
          </p>
          <p>
            <span>
              第一次被举报:
              <span class="is-size-6"><Time v-if="cheater.createDatetime" :time="cheater.createDatetime"></Time></span>
            </span>
            <span>
              最近更新:
              <span class="is-size-6"><Time v-if="cheater.updateDatetime" :time="cheater.updateDatetime"></Time></span>
            </span>
          </p>

          <div style="margin-top: .4rem;">
            <h2>Ta的被举报的游戏：</h2>
            <div>
              <router-link v-for="g in games" :key="g.game" :to="{name: 'cheaters'}" >
                {{ g.game }}
              </router-link>
            </div>
          </div>

          <div v-show="cheater.originId" style="margin-top: .4rem;">
            <h2>
              Ta的相关战绩链接：
            </h2>
            <p v-for="g in games" :key="g.game">
              <Tag>
                {{g.game}}
              </Tag>
              <a v-show="`${g.game}` === 'bf1'" target="_blank" :href="`https://battlefieldtracker.com/bf1/profile/pc/${cheater.originId}`">
                battlefieldtracker
              </a>
			  <a v-show="`${g.game}` === 'bf1'" target="_blank" :href="`http://bf1stats.com/pc/${cheater.originId}`">
                bf1stats
              </a>
              <a v-show="`${g.game}` === 'bf1'" target="_blank" :href="`https://www.247fairplay.com/CheatDetector/${cheater.originId}`">
                247fairplay
              </a>
              <a v-show="`${g.game}` === 'bfv'" target="_blank" :href="`https://battlefieldtracker.com/bfv/profile/origin/${cheater.originId}`">
                battlefieldtracker
              </a>
			  <a v-show="`${g.game}` === 'bfv'" target="_blank" :href="`https://www.247fairplay.com/CheatDetector/${cheater.originId}`">
                247fairplay
              </a>
            </p>

            <a v-if="cheater.trackerShot" :href="cheater.trackerShot" target="_blank">bf1tracker数据截图</a>
            <a v-if="cheater.trackerWeaponShot" :href="cheater.trackerWeaponShot" target="_blank">bf1tracker武器截图</a>
            <a v-if="cheater.bf1statsShot" :href="cheater.bf1statsShot" target="_blank">bf1stats数据截图</a>
          </div>

          <div v-show="cheater.avatarLink" style="margin-top: .4rem;">
            <h2>
              Ta的origin头像：
            </h2>
            <img :src="cheater.avatarLink" alt="avatar" width="208" height="208">
          </div>

          <div v-show="origins.length" style="margin-top: .4rem;">
            <h2>Ta的历史ID：</h2>
            <table>
              <thead>
                <tr>
                  <td><b>更新时间</b></td>
                  <td><b>ID</b></td>
                </tr>
              </thead>
              <tbody>
                <tr v-for="origin in origins" :key="origin.id">
                  <td>
                    <Time :time="origin.createDatetime"></Time>
                  </td>
                  <td>{{ origin.cheaterGameName }}</td>
                </tr>
              </tbody>
            </table>
          </div>

          <div style="margin-top: .4rem;">
            <h2>管理员处理历史：</h2>
            <table>
              <thead>
                <tr>
                  <td><b>处理时间</b></td>
                  <td><b>操作</b></td>
                </tr>
              </thead>
              <tbody>
              </tbody>
            </table>
          </div>

          <div style="margin-top: .4rem;">
            <p class="hint">
              若发现 链接失效 或 改名，请点击
              <a href="#" @click.prevent="updateCheaterInfo">更新资料</a>，
              bfban.com 有能力<b>永久追踪</b>被举报者的行踪。
            </p>
            <p class="hint">
              服务器也会定期抓取、更新被举报人的信息
            </p>
            <p class="hint">
              历史ID也可以用来搜索
            </p>
          </div>

          <Spin size="large" fix v-show="updateUserInfospinShow"></Spin>
        </div>

        <div>
          <h2 style="margin: 1rem 0;">时间线</h2>
          <TimelineItem pending v-for="l in timelineList" :key="l.createDatetime">

            <div v-if="l.type === 'report'" class="timeline-content">
              <div class="timeline-time">
                <Time :time="l.createDatetime"></Time>

                <router-link :to="{name: 'account', params: {uId: `${l.uId}`}}">
                  <Tag v-if="l.privilege === 'admin'" color="success">
                    管理员
                  </Tag>
                  <b>{{l.username}}</b>
                </router-link>
                举报

                <router-link :to="{name: 'cheater', ouid: `${l.originUserId}`}">
                  {{ l.cheaterGameName }}
                </router-link>

                在

                <router-link :to="{name: 'cheaters', query: {game: `${l.game}`} }">
                  {{l.game}}
                </router-link>

                游戏中

                <b>
                  {{convertCheatMethods(l.cheatMethods || '')}}
                </b>
              </div>

              <p v-if="l.bilibiliLink">
                <Tag color="primary">
                  视频链接
                </Tag>
                <a :href="l.bilibiliLink" target="_blank">{{ l.bilibiliLink }}</a>
              </p>
              <div v-if="l.description" v-html="l.description" class="description">
              </div>

              <p v-if="isLogin">
                <a href="#" :data-floor="`${l.floor}`" :data-user-id="`${l.userId}`" @click.prevent="handleReply">回复</a>
              </p>
            </div>

            <div v-if="l.type === 'verify'" class="timeline-content bookmark" :id="`user-verify-cheater-${l.id}`">
              <div class="timeline-time">
                <Time v-if="l.createDatetime" :time="l.createDatetime"></Time>
                <router-link :to="{name: 'account', params: {uId: `${l.uId}`}}">
                  <Tag v-if="l.privilege === 'admin'" color="success">
                    管理员
                  </Tag>
                  <b>{{l.username}}</b>
                </router-link>

                认为

                <Tag color="warning">
                  {{ handleStatus(l.status) }}
                </Tag>

                <span v-if="l.cheatMethods">
              ，
              作弊方式

              <b>
                {{convertCheatMethods(l.cheatMethods || '')}}
              </b>
            </span>
              </div>

              <div v-html="l.suggestion" class="description"></div>

              <p v-show="isAdmin && cheater.status !== '1' && l.status === '1' && !isSelf(l.userId)">
                <a href="#"
                   @click.prevent.stop="doConfirm"
                   :data-user-verify-cheater-id="l.id"
                   :data-cheat-methods="l.cheatMethods"
                   :data-user-verify-cheater-username="l.username">

                  <Icon type="md-thumbs-up" />
                  赞同上处理 并 石锤
                </a>
              </p>

              <p v-if="isLogin">
                <a href="#" :data-floor="`${l.floor}`" :data-user-id="`${l.userId}`" @click.prevent="handleReply">回复</a>
              </p>
            </div>

            <div v-if="l.type === 'confirm'" class="timeline-content">
              <div class="timeline-time">
                <Time v-if="l.createDatetime" :time="l.createDatetime"></Time>

                <router-link :to="{name: 'account', params: {uId: `${l.uId}`}}">
                  <Tag v-if="l.privilege === 'admin'" color="success">
                    管理员
                  </Tag>
                  <b>{{l.username}}</b>
                </router-link>
                赞同了
                <a @click.stop.prevent="jumpToBookmark" :data-hash="`#user-verify-cheater-${l.userVerifyCheaterId}`">
                  # 该决定
                </a>
                ，作弊方式

                <b>
                  {{ convertCheatMethods(l.cheatMethods || '') }}
                </b>
              </div>

              <p v-if="isLogin">
                <a href="#" :data-floor="`${l.floor}`" :data-user-id="`${l.userId}`" @click.prevent="handleReply">回复</a>
              </p>
            </div>

            <div v-if="l.type === 'reply'" class="timeline-content">
              <div class="timeline-time">
                <Time v-if="l.createDatetime" :time="l.createDatetime"></Time>

                <router-link v-if="l.foo" :to="{name: 'account', params: {uId: `${l.fooUId}`}}">

                  <Tag v-if="l.fooPrivilege === 'admin'" color="success">
                    管理员
                  </Tag>
                  <b>{{l.foo}}</b>
                </router-link>
                回复
                <router-link v-if="l.bar" :to="{name: 'account', params: {uId: `${l.barUId}`}}">

                  <Tag v-if="l.barPrivilege === 'admin'" color="success">
                    管理员
                  </Tag>
                  <b>{{l.bar}}</b>
                </router-link>
              </div>

              <div v-html="l.content" class="description"></div>

              <p v-if="isLogin">
                <a href="#" :data-floor="`${l.floor}`" :data-user-id="`${l.userId}`" @click.prevent="handleReply">回复</a>
              </p>
            </div>

          </TimelineItem>
        <Page :page-size="limit" show-total :current="page" @on-change="handlePageChange" :total="total" class="page" size="small" />

        </div>

        <div v-if="isLogin">
          <p class="hint">如果有新的证据图片或视频需要补充，可以再次举报。回复功能只能回复文本或链接。如需回复图片，请移步这里 <a href="https://sm.ms/" target="_blank">上传图片 </a>，然后复制图片链接进行回复。</p>
          <Form :label-width="80" style="position: relative;">
            <p>
              <Input @on-keydown="handleCmdEnter($event, 'reply')" v-model="reply.content" type="textarea" :autosize="{minRows: 2}" placeholder="说点什么吧。。。" />
            </p>
            <Button type="primary" @click.stop.prevent="doReply">回复</Button>

            <Spin size="large" fix v-show="replySpinShow"></Spin>
          </Form>
        </div>

        <div v-if="cheater.status === '1'">
          <Divider />
        </div>

        <p class="hint">注册登录后才可以参与讨论</p>

        <div v-if="isAdmin">
          <Divider>管理员专区</Divider>
          <p class="hint">不要轻易下判断，如果不能做出处理判断，就使用上方回复 参与讨论，等待举报者回复</p>
          <p class="hint">管理员的任何处理操作都会对作弊者的 现有状态 造成改变，如果不是100%确定，请使用回复留言讨论</p>

          <h2 style="margin: 1rem 0;">处理意见</h2>

          <Form :label-width="80" ref='verifyForm' style="position: relative;">
            <FormItem label="意见">
              <Select v-model="verify.status">
                <Option value="1">石锤</Option>
                <Option value="2">嫌疑再观察</Option>
                <Option value="3">没有问题不是挂</Option>
                <Option value="4">回收站</Option>
              </Select>
            </FormItem>

            <FormItem v-show="verify.status === '1'" label="作弊方式">
              <CheckboxGroup v-model="verify.checkbox">
                <Checkbox v-for="method in cheatMethodsGlossary" :key="method.value" :label="method.value">
                  {{ method.label }}
                </Checkbox>
              </CheckboxGroup>
            </FormItem>

            <FormItem label="理由">
              <Input @on-keydown="handleCmdEnter($event, 'verify')" v-model="verify.suggestion" type="textarea" :autosize="{minRows: 2}" placeholder="必填" />
            </FormItem>

            <FormItem>
              <Button type="primary" @click.stop.prevent="doVerify">提交</Button>
            </FormItem>

            <Spin size="large" fix v-show="verifySpinShow"></Spin>
          </Form>
        </div>


        <Modal
          v-model="replyModal"
          title="回复"
          ok-text="提交"
          cancel-text="取消"
          @on-ok="doReply"
          @on-cancel="cancelReply">
          <div v-if="isLogin">
            <Form :label-width="80" ref='replyForm' style="position: relative;">
              <Input @on-keydown="handleCmdEnter($event, 'reply')" v-model="reply.content" type="textarea" :autosize="{minRows: 2}" placeholder="说点什么吧。。。" />
            </Form>
          </div>
          <div v-else>请登录后参与回复</div>
        </Modal>

        <Spin size="large" fix v-show="spinShow"></Spin>
      </div>
      <div v-else>404 不存在</div>

    </div>
  </div>

</template>

<script>
import ajax from '@/mixins/ajax';
import {
  checkIdExist,
  getCheaterStatusLabel,
  formatTextarea,
  convertDatetimeToUserTimeZone,
  cheatMethodsGlossary,
  convertCheatMethods,
  waitForAction,
  replaceImgSrcToDataSrc
} from "@/mixins/common";

export default {
  data() {
    return {
      cheater: {
        originId: '',
      },
      origins: [],
      games: [],
      timelineList: [],
      verify: {
        status: '1',
        checkbox: [],
        suggestion: '',
      },
      spinShow: true,
      idExist: false,

      verifySpinShow: false,

      reply: {
        cheaterId: '',
        userId: '',
        content: '',
        toFloor: '',
        toUserId: '',
      },
      replySpinShow: false,

      gameName: '',

      isCheaterExist: true,

      replyModal: false,

      cheatMethodsGlossary,

      updateUserInfospinShow: false,
    }
  },
  watch: {
    '$route': 'loadData',
  },
  created() {
    this.loadData();
  },
  updated() {
    new LazyLoad({})
  },
  methods: {
    loadData() {
      const originUserId = this.$route.params.ouid;

      ajax({
        method: 'get',
        url: `/cheaters/${originUserId}`,
      })
      .then((res) => {
        this.spinShow = false;

        const d = res.data;
        let { cheater, games, origins, reports, verifies, confirms, replies } = d.data;

        if (cheater.length === 0) {
          this.isCheaterExist = false;
          return false;
        } else {
          this.isCheaterExist = true;
        }

        this.cheater = cheater[0];
        this.origins = _.sortBy(origins, 'createDatetime').reverse();
        this.games = _.sortBy(games, 'game');


        reports = _.each(reports, (v, k, l) => {
          v['type'] = 'report'
        });
        verifies = _.each(verifies, (v, k, l) => {
          v['type'] = 'verify'
        });
        confirms = _.each(confirms, (v, k, l) => {
          v['type'] = 'confirm'
        });
        replies = _.each(replies, (v, k, l) => {
          v['type'] = 'reply'
        });

        // img src => data-src
        reports = _.each(reports, (v) => {
          v['description'] = replaceImgSrcToDataSrc(v['description']);
        });
        replies = _.each(replies, (v, k, l) => {
          v['content'] = replaceImgSrcToDataSrc(v['content']);
        });

        let timelineList = reports.concat(verifies, confirms, replies);

        timelineList = _.sortBy(timelineList, (v) => {
          return (new Date(v.createDatetime)).getTime();
        });

        timelineList = _.each(timelineList, (v, k) => {
          v['floor'] = k + 1;
        });

        // render timeline
        this.timelineList = timelineList;

      });
    },
    jumpToBookmark(e) {
      let hash = e.target.dataset.hash;
      let el = document.querySelector(hash);

      // offset top 一层层 依次累加
      let offsetY = el.offsetParent.offsetTop + el.offsetParent.offsetParent.offsetTop + el.offsetParent.offsetParent.offsetParent.offsetTop - document.querySelector('header').offsetHeight;

      document.documentElement.scrollTop = offsetY;

      el.setAttribute('style', 'background: rgba(255, 153, 1, 0.15)');
      setTimeout(function() {
        el.setAttribute('style', 'transition: background 1s ease .5s;')
      }, 100);
    },
    handleStatus: getCheaterStatusLabel,
    convertCheatMethods,
    async doVerify() {
      const {status} = this.verify;
      let { suggestion } = this.verify;
      const cheatMethods = this.verify.checkbox.join(',');
      const { originUserId } = this.cheater;

      if ( (status === '1' && cheatMethods === '') || suggestion.trim() === '') {
        this.$Message.warning('请填写完整');
        return false;
      }

      // JUST before ajax
      this.verifySpinShow = true;
      const {data: statusData} = await ajax({
        method: 'post',
        url: '/cheaters/status',
        data: {
          originUserId,
        }
      });
      if (statusData.error) {
        this.$Message.error(statusData.msg);
        return false;
      }
      if (statusData.error === 0 && statusData.status === '1') {
        if (!confirm(`当前是 石锤 状态，你确定要处理成 ${getCheaterStatusLabel(status)} 吗？`)) {
          this.verifySpinShow = false;
          return false;
        }
      }

      suggestion = formatTextarea(suggestion);


      ajax({
        method: 'post',
        url: '/cheaters/verify',
        data: {
          status,
          suggestion,
          cheatMethods,
          originUserId,
        }
      })
      .then((res) => {
        this.verifySpinShow = false;

        const d = res.data;
        if (res.data.error === 0) {
          // reset verifyForm
          this.verify.status = '1';
          this.verify.suggestion = '';
          this.verify.checkbox = [];

          const { id, userId, createDatetime, status, suggestion, username, cheatMethods, privilege } = d.data;

          this.cheater.status = status;

          this.timelineList.push({
            type: 'verify',
            id,
            userId,
            createDatetime: convertDatetimeToUserTimeZone(createDatetime),
            // fix bug
            status: status === '6' ? '1' : status,
            suggestion,
            cheatMethods,
            username,
            privilege,
          });

          this.$Message.success('提交成功');
        } else {
          this.$Message.error('提交失败 ' + d.msg);
        }
      })
    },
    doConfirm(e) {
      const { userVerifyCheaterId, userVerifyCheaterUsername, cheatMethods } = e.target.dataset;
      const { userId } = this.$store.state.user;
      const originUserId = this.$route.params.ouid;

      ajax({
        method: 'post',
        url: '/cheaters/confirm',
        data: {
          userVerifyCheaterId,
          userId,
          originUserId,
          cheatMethods,
        }
      })
      .then((res) => {
        let d = res.data;

        if (d.error === 0) {
          const { createDatetime } = d.data;

          this.cheater.status = '1';

          this.timelineList.push({
            type: 'confirm',
            userId,
            userVerifyCheaterId,
            createDatetime: convertDatetimeToUserTimeZone(createDatetime),
            cheatMethods,
            username: this.$store.state.user.username,
          })
        } else {
          this.$Message.warning(d.msg);
        }
      })
    },
    isSelf(id) {
      const userId = this.$store.state.user.userId;

      return (parseInt(userId) === parseInt(id))
    },
    handleReply(e) {
      const { floor, userId } = e.target.dataset;

      this.reply.toFloor = floor === 'undefined' ?  '' : floor;
      this.reply.toUserId = userId === 'undefined' ? '' : userId;

      // open reply modal
      this.replyModal = true;
    },
    cancelReply() {
      this.reply = {};
    },
    doReply() {
      this.replySpinShow = true;

      const cheaterId = this.cheater.id;
      const userId = this.$store.state.user.userId;
      const { toFloor, toUserId } = this.reply;
      const { originUserId } = this.cheater;
      let {content = ''} = this.reply;

      content = formatTextarea(content);

      let data = {
        cheaterId,
        userId,
        content,
        originUserId,
      };

      if (toFloor) {
        data['toFloor'] = toFloor;
      }
      if (toUserId) {
        data['toUserId'] = toUserId;
      }

      ajax({
        method: 'post',
        url: '/cheaters/reply',
        data,
      })
      .then((res) => {
        this.replySpinShow = false;

        const d = res.data;

        if (d.error === 0) {
          const { createDatetime, content, status } = d.data;
          this.$Message.success('回复成功');

          // reset reply
          this.cancelReply();

          const foo = this.$store.state.user.username;
          const fooUId = this.$store.state.user.uId;
          const bar = '';
          const barUId = '';
          this.timelineList.push({
            type: 'reply',

            createDatetime: convertDatetimeToUserTimeZone(createDatetime),
            content,
            foo,
            fooUId,
            bar,
            barUId,
          });
          this.cheater.status = status;
        } else {
          this.$Message.error(d.msg);
        }
      });
    },
    handleCmdEnter(e, type) {
      if ( (e.metaKey || e.ctrlKey) && e.keyCode == 13 )  {
        switch(type) {
          case 'reply':
            this.doReply()
            break;
          case 'verify':
            this.doVerify();
            break;
        }
      }
    },
    updateCheaterInfo(e) {
      waitForAction.call(e.target, 60);

      if (!Boolean(this.$store.state.user)) {
        this.$Message.error('请登录');
        return false;
      }

      this.updateUserInfospinShow = true;
      const { originUserId } = this.cheater;
      ajax({
        method: 'post',
        url: '/cheaters/updateCheaterInfo',
        data: {
          originUserId,
        }
      }).then((res) => {
        this.updateUserInfospinShow = false;

        const d = res.data;
        if (d.error === 0) {
          const {cheaterGameName: originId, originUserId, avatarLink} = d.data.origin;

          this.cheater.originId = originId;
          this.cheater.originUserId = originUserId;
          this.cheater.avatarLink = avatarLink;

          this.origins.unshift(d.data.origin);

          this.$Message.success('更新完成');
        } else {
          this.$Message.error(d.msg);
        }
      });
    },
  },
  computed: {
    isVerified() {
      return this.verifies.length > 0
    },
    isAdmin() {
      const user = this.$store.state.user;

      const is = user ? user.userPrivilege !== 'normal' : false;
      return Boolean(is);
    },
    isLogin() {
      return Boolean(this.$store.state.user);
    }
  }
}
</script>

<style lang="scss">
  .cheater-desc {
    max-width: 100%;
    width: 34rem;
  }
  .description {
    color: rgba(0, 0, 0, 0.8);
    font-size: .8rem;
    line-height: 1.4rem;

    img, video {
      max-width: 100%;
    }
  }

  .timeline-time {
    color: #00000073;
  }
  .ivu-time {
    margin-right: .6rem;
  }
  .timeline-content {
    position: relative;

    // force to wrap
    overflow-wrap: break-word;
    word-wrap: break-word;
  }
  .ivu-timeline-item {
    padding: 1rem 0;
    &:hover {
      background-color: #eaeaea9c;
    }
  }
  .ivu-timeline-item-content {
    padding: 0 .6rem 0 1.2rem;
  }
  .ivu-timeline-item-tail {
    top: 1rem;
  }

  /*https://stackoverflow.com/a/38106970/875788*/
  /*.bookmark::before {*/
    /*display: block;*/
    /*content: " ";*/
    /*margin-top: -4rem;*/
    /*height: 4rem;*/
    /*visibility: hidden;*/
  /*}*/

  /*let element = document.querySelector('#user-verify-cheater-7')*/
  /*element.offsetTop - element.scrollTop + element.clientTop*/
</style>

