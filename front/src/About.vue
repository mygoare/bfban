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
        目前主要由下列力量组成：
        <b>私服主人</b> ，<b>战队队长</b> 以及 <b>战地UP</b>；      创始人是CDN战队队长凯撒Caesar。
      </p>
      <p>
        联BAN局创建以来，
        联合诸多玩家一起抵制外挂，
        成功打击了近500名外挂和外挂主播；
        抵制外挂之战，不仅在游戏内，更是一场敌暗我明的幕外战争。
      </p>
      <br>
      <p>
        <b>目前加盟服务器，共 {{clan.length}} 个：</b>
        （ 搜索 <b>LB</b> 都可以找到加入联BAN局的服务器 ）
      </p>

      <div class="clan">
        <p v-for="d in clan" :key="d">{{ d }}</p>
      </div>
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
          'Lowb',
          'kosuzu',
          'ROB',
          'HERO',
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
      flex-grow: 1;
      flex-basis: 20%;
      flex-shrink: 0;
    }
  }
</style>
