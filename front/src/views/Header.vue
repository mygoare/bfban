<template>
    <header>
      <div class="header-container container">
        <div class="nav">
          <router-link class="mobile-hide" :to="{name: 'home'}">
            <Icon type="md-home" />
            {{ $t("header.index") }}
          </router-link>
          <router-link class="mobile-hide" :to="{name: 'cheaters', query: { status: '100' }}">
            <Icon type="md-list-box" />
            {{$t("header.cheaters")}}
          </router-link>
          <router-link class="mobile-hide" :to="{name: 'report'}">
            <Icon type="ios-megaphone" />
            {{$t("header.report")}}
          </router-link>
          <router-link class="mobile-hide" :to="{name: 'about'}">{{$t("header.about")}}</router-link>
          <router-link class="mobile-hide" v-if="isAdmin" :to="{name: 'dashboard'}">
            <Icon type="md-cog" />
            {{$t("header.dashboard")}}
          </router-link>

          <router-link class="desktop-hide" :to="{name: 'home'}">
            <Icon size="24" type="md-home" />
          </router-link>
          <router-link class="desktop-hide" :to="{name: 'cheaters', query: { status: '100' }}">
            <Icon size="24" type="md-list-box" />
          </router-link>
          <router-link class="desktop-hide" :to="{name: 'report'}">
            <Icon size="24" type="ios-megaphone" />
          </router-link>
          <router-link class="desktop-hide" v-if="isAdmin" :to="{name: 'dashboard'}">
            <Icon size="24" type="md-cog" />
          </router-link>
          <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=150 height=52 src="//music.163.com/outchain/player?type=2&id=1341520969&auto=0&height=32"></iframe>
        </div>
        <div class="search mobile-hide">
          <Input clearable search placeholder="支持搜索历史ID啦..." v-model="searchVal" @on-search="handleSearch" />
        </div>
        <div class="nav">
          <router-link v-show="!isLogin" class="mobile-hide" :to="{name: 'signin'}">
            <Icon type="md-log-in" />
            {{$t("header.signin")}}
          </router-link>
          <router-link v-show="!isLogin" class="mobile-hide" :to="{name: 'signup'}">
            <Icon type="md-person-add" />
            {{$t("header.signup")}}
          </router-link>

          <router-link v-show="!isLogin" class="desktop-hide" :to="{name: 'signin'}">
            <Icon type="md-log-in" size="24" />
          </router-link>
          <router-link v-show="!isLogin" class="desktop-hide" :to="{name: 'signup'}">
            <Icon type="md-person-add" size="24" />
          </router-link>

          <router-link class="nav-username mobile-hide" v-if="isLogin" :to="{name: 'account', params: { uId: `${currentUser.uId}` }}">
            <Badge dot>
              <Icon type="md-person" />
              {{ currentUser.username }}
            </Badge>
          </router-link>
          <router-link class="nav-username desktop-hide" v-if="isLogin" :to="{name: 'account', params: { uId: `${currentUser.uId}` }}">
            <Badge dot>
              <Icon size="24" type="md-person" />
            </Badge>
          </router-link>

          <a class="nav-signout mobile-hide" v-show="isLogin" href="#" @click.stop.prevent="signout">
            <Icon type="md-log-out"></Icon>
            {{$t("header.signout")}}
          </a>
          <a class="nav-signout desktop-hide" v-show="isLogin" href="#" @click.stop.prevent="signout">
            <Icon type="md-log-out" size="24"></Icon>
          </a>

        </div>
      </div>

      <Modal
        v-model="searchModal"
        title="检索"
        @on-ok="ok"
        @on-cancel="cancel">
        <div style="position: relative">
          <p style="font-size: 1rem;">
            检索的ID为：{{searchVal}}
          </p>
          <br>
          <p>
            检索结果：
          </p>
          <div v-if="cheaters.length !== 0">
            <p v-for="cheater in cheaters" :key="cheater.id">
              <router-link :to="{name: 'cheater', params: {ouid: `${cheater.originUserId}`}}">
                {{cheater.originId}}
              </router-link>
            </p>
          </div>
          <div v-else>无</div>

          <Spin size="large" fix v-show="modalSpinShow"></Spin>

        </div>
      </Modal>
    </header>
</template>

<script>
  import ajax from '@/mixins/ajax';

  export default {
  data() {
    return {
      searchModal: false,
      searchVal: '',
      cheaters: [],

      modalSpinShow: false,
    }
  },
  methods: {
    handleSearch() {
      const val = this.searchVal.trim();

      if (val === '') return false;

      this.searchModal = true;
      this.modalSpinShow = true;

      ajax({
        method: 'get',
        url: `/search?id=${val}`,
      })
      .then((res) => {
        this.modalSpinShow = false;

        const d = res.data;
        if (d.error === 0) {

          const { cheaters } = d.data;
          this.cheaters = cheaters;
        }
      })
    },
    signout() {
      ajax({
        method: 'get',
        url: '/account/signout'
      })
      .then((res) => {
        const d = res.data;

        if (d.error === 0) {
          this.$store.dispatch('signout').then(() => {
            this.$router.push('/');

            this.$Message.success('注销成功');
          })
        }
      })
    }
  },
  computed: {
    isLogin() {
      return Boolean(this.$store.state.user);
    },
    isAdmin() {
      const user = this.$store.state.user;

      const is = user ? user.userPrivilege !== 'normal' : false;
      return Boolean(is);
    },
    currentUser() {
      return this.$store.state.user;
    }
  }
}

let styles = `
   background-color: #fff;
   box-shadow: 0 0 10px 0px #676767;
`;
window.addEventListener('scroll', _.throttle(function(e) {
  let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;

  if (scrollTop > 24) {
    document.querySelector('header').setAttribute('style', styles);
  } else {
    document.querySelector('header').removeAttribute('style');
  }
}, 300));

</script>

<style lang="scss">
  header {
    background-color: rgba(252,252,252,0.8);
    width: 100%;
    height: auto;
    box-shadow: 0 0 4px rgba(0,0,0,0.15);

    position: fixed;
    z-index: 1000;
  }
  .header-container {
    display: flex;
    justify-content: space-between;
    align-items: center;

    .search {
      display: flex;
      flex-grow: 0;
      flex-shrink: 1;
      flex-basis: 30%;
    }
  }
  .nav {
    display: flex;
    align-items: center;
    padding: 0 .4rem;
    a {
      padding: 1rem .6rem;
      &:hover {
        background-color: rgba(251, 251, 251, 0.8);
        color: black;
      }
    }
  }
  .nav-username {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    max-width: 6rem;
    flex-grow: 0;
  }
  .nav-signout {
    flex-shrink: 0;
  }

  @media screen and (min-width: 1088px) {
    .nav-username {
      width: 100%;
    }
  }

  .ivu-badge-dot {
    left: -4px;
  }
</style>

