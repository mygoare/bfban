<template>
    <header>
      <div class="header-container container">
        <div class="nav">
          <router-link :to="{name: 'home'}">{{ $t("header.index") }}</router-link>

          <router-link :to="{name: 'cheaters', query: { status: '100' }}">{{$t("header.cheaters")}}</router-link>

          <router-link class="" :to="{name: 'report'}">{{$t("header.report")}}</router-link>

          <router-link class="mobile-hide" :to="{name: 'about'}">{{$t("header.about")}}</router-link>


        </div>
        <div class="search mobile-hide">
          <Input clearable search placeholder="检索" v-model="searchVal" @on-search="handleSearch" />
        </div>
        <div class="nav">
          <router-link v-show="!isLogin" :to="{name: 'signin'}">{{$t("header.signin")}}</router-link>
          <router-link v-show="!isLogin" :to="{name: 'signup'}">{{$t("header.signup")}}</router-link>

          <router-link class="nav-username" v-if="isLogin" :to="{name: 'account', params: { uId: `${currentUser.uId}` }}">
            <Badge dot>
              {{ currentUser.username }}
            </Badge>
          </router-link>
          <a class="nav-signout" v-show="isLogin" href="#" @click.stop.prevent="signout">{{$t("header.signout")}}</a>

          <router-link v-if="isAdmin" :to="{name: 'dashboard'}">
            {{$t("header.dashboard")}}
          </router-link>
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
              <router-link :to="{name: 'cheater', params: {game: `${cheater.game}`, uid: `${cheater.uId}`}}">
                <Tag>
                  {{ cheater.game }}
                </Tag>
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

      axios({
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
      axios({
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

const scrollEvent = _.throttle(function(e) {
  let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;

  if (scrollTop > 24) {
    document.querySelector('header').setAttribute('style', styles);
  } else {
    document.querySelector('header').removeAttribute('style');
  }
}, 300)
window.addEventListener('scroll', scrollEvent)

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
    /*width: 6rem;*/
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

