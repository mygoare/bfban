<template>
  <div class="content">
      <Divider>{{$t("home.bulletin.title")}}</Divider>

      <div class="md" v-html="data"></div>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        data: marked(`
**网站开发**: [mygoare](https://mygoare.com)
**BFBAN APP下载**: [cabbagelol](https://app.bfban.com/public/www/)
**网站服务器提供**: [AWSL服务器](https://battlefield.now.sh/)
        `, {breaks: true, gfm: true, smartLists: true})
      }
    }
  }
</script>

<style lang="scss">
  .md {
    ul {
      margin-left: 1rem;
    }
    p {
      margin: .4rem 0;
    }
  }
</style>
