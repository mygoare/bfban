<template>
  <div class="container">
    <div class="content">
      <Divider>{{$t("about.title")}}</Divider>

      <img class="logo" src="https://i.loli.net/2018/11/03/5bdd8e977b10d.png" alt="">


      <h2>联BAN调查局</h2>
      <p>
        诞生于2018年1月29日；
        是战地玩家一起组建的 <b>抵制外挂</b> 组织。
      </p>
      <p>
        目前主要成员组成：
        <ul style="padding: .5rem 1rem .5rem;">
          <li>私服主</li>
          <li>战队队长</li>
          <li>战地UP</li>
        </ul>
      </p>

      <p>
        <b>目前加盟服务器，共 {{clan.length}} 个：</b>
        （ 搜索 <b>LB</b> 都可以找到加入联BAN局的服务器 ）
      </p>

      <div class="clan">
        <p v-for="d in clan" :key="d.id">{{ d }}</p>
      </div>

      <br>

      <h2>bfban.com 网站</h2>
      <p>
        <a href="https://bfban.com">bfban.com</a> 网站依托于联BAN调查局，最初想法起源于热心玩家，由 mygoare 开发，于 2018年11月4日 上线‘
      </p>
      <p>主要为广大玩家提供举报、列举、搜索、追踪、讨论等服务；</p>

      <br>
      <h2>联系我们</h2>
      <p>
        <a target="_blank" href="https://jq.qq.com/?_wv=1027&k=MaiwoIsn">
          1104053054
        </a>

        举报QQ群
      </p>
      <p>
          mygoare@gmail.com 开发者邮箱
      </p>
    </div>
  </div>

</template>

<script>
  export default {
    data() {
      return {
        clan: [
          '1975',
          'CDN',
          'FATE',
          'HS',
          'SAK',
          'TNT',
          'Mua',
          'More27',
          'XJBD',
          'CNH',
          'KOI',
          'RUSH',
          'BOA',
          'pb',
          'coco',
          'WSG',
          'Lion',
          'LowB',
          'kosuzu',
          'ROB',
          'HERO',
          'b1br',
        ],
      }
    }
  }
</script>

<style lang="scss">
  .logo {
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 10rem;
    border-radius: 10rem;
  }

  .clan {
    display: flex;
    flex-wrap: wrap;

    p {
      flex-grow: 0;
      flex-basis: 20%;
      flex-shrink: 0;
    }
  }
</style>
