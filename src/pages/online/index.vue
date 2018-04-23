<template>
  <div>
    <header-top title="网上商城" :back="false" :login="!loginAccount">注册|登录</header-top>
    <main class='scroll-content'>
      <section>
        <yd-slider :loop="false" :autoplay="3000">
          <yd-slider-item v-for="item in info.indexAds" :key="item.id">
            <router-link :to="{name:'Product',query:{id:item.address.substring(item.address.lastIndexOf('/')+1),tips:0}}">
              <img :src="item.photo" :alt="item.names">
            </router-link>
          </yd-slider-item>
        </yd-slider>
      </section>
      <section class="vocher-container">
        <div><img :src="getLocalImg('fast.jpg')"></div>
        <div class="flex align-center news-box">
          <div class="rolltip">今日资讯：</div>
          <yd-rollnotice autoplay="4000" class="flex-1">
            <yd-rollnotice-item v-for="(item,index) in info.newList&&info.newList.result" :key="index">
              <router-link :to="{name:'NewsDetail',query:{id:item.id}}" class="flex align-center" style="width:100%;">
                <span class="news-tag">{{item.source}}</span>
                <span class="news-title flex-1 fs-14">{{item.title}}</span>
              </router-link>
            </yd-rollnotice-item>
          </yd-rollnotice>
          <router-link :to="{name:'News',params:{update:true}}">更多></router-link>
        </div>
      </section>
      <section class="platform-container flex just-around">
        <div v-for="(item,index) in platform" :key="index" class="platform-item" @click="navigate(item.link)">
          <img :src="getLocalImg(item.img)">
        </div>
      </section>
      <section class="pd-list" v-for="(item,index) in pds" :key="index">
        <yd-cell-group>
          <yd-cell-item arrow type="label" @click.native="goProducts(item.id)">
            <span slot="icon" class="iconfont self-libao" style="color:#f98e25;font-size:20px;"></span>
            <span slot="left">{{item.label}}</span>
            <span slot="right">更多</span>
          </yd-cell-item>
        </yd-cell-group>
        <ul class="flex just-between">
          <router-link :to="{path:'/online/product',query:{id:pd.id}}" v-for="pd in item.list" :key="pd.id" tag="li" class="pd-item">
            <div class="img-cover">
              <img :src="pd.imgUrl" :alt="pd.name">
            </div>
            <div class="title flex align-center">
              <h3>{{pd.name}}</h3>
            </div>
            <div class="price" v-if="pd.isCanUserCou=='1'">
              <span>￥{{formatPrice(pd.pointNicePrice)}}</span>+
              <span>{{pd.price}}积分</span>
            </div>
            <div class="price" v-else-if="pd.isCanUserCou=='2'">
              <span>￥{{formatPrice(pd.pointNicePrice)}}</span>+
              <span>{{pd.price}}责任金额</span>
            </div>
            <div class="price" v-else-if="pd.isCanUserCou=='3'">
              <span>{{pd.price}}代金券</span>
            </div>
            <div class="price" v-else>
              <span>￥{{formatPrice(pd.price)}}</span>
            </div>
          </router-link>
        </ul>
      </section>
    </main>
    <footer-bar></footer-bar>
  </div>
</template>
<script>
import { mapState } from "vuex";
import HeaderTop from "components/header/index";
import FooterBar from "components/footer/index";
import { mixin, getStore, setStore, localImg } from "components/common/mixin";
import { onlineInH5, findAppUpgredeByType } from "../../api/index";
export default {
  name: "Online",
  data() {
    return {
      oldBack: mui.back,
      info: {},
      pds: [],
      loginAccount: false,
      platform: [
        { img: "jingdong.png", link: "/online/jdindex" },
        { img: "tianmao.jpg", link: "/online/tmindex" },
        { img: "taobao.jpg", link: "/online/tmindex" },
        { img: "lianying.png", link: "/online/jdself" }
      ],
      type: "", //APP环境
      curVersion: "" //app版本
    };
  },
  components: { HeaderTop, FooterBar },
  computed: { ...mapState(["account", "member"]) },
  mixins: [mixin, localImg],
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.plusReady();
    });
  },
  beforeRouteLeave(to, from, next) {
    mui.back = this.oldBack;
    next();
  },
  created() {
    this.init();
    this.getInfo();
  },
  mounted() {
    document.addEventListener("plusready", this.getVersion, false);
  },
  activated() {
    if (getStore("account") && getStore("account").length > 0) {
      this.loginAccount = true;
      this.$store.commit("SET_ACCOUNT", getStore("account"));
      if (!this.member) {
        this.$store.dispatch("getInfo");
      }
    } else {
      this.loginAccount = false;
    }
    if (this.$store.state.positions[this.$route.path]) {
      document.querySelector("main").scrollTop = this.$store.state.positions[
        this.$route.path
      ];
    }
  },
  methods: {
    init() {
      let u = navigator.userAgent;
      let isAndroid = u.indexOf("Android") > -1; //android终端
      let isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
      this.type = isiOS ? "1" : "0";
    },
    getInfo() {
      let vm = this;
      this.$dialog.loading.open();
      mui.ajax({
        url: onlineInH5,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          token: md5(`gjfengonlineInH5`)
        },
        success(res) {
          vm.$dialog.loading.close();
          let _pds = [];
          Object.entries(res.result.indexProducts).forEach((value, index) => {
            var item = {};
            var desc = value[0].split(/[;|~]/);
            item.label = desc[0];
            item.icon = desc[1];
            item.id = desc[2];
            item.list = value[1];
            _pds.push(item);
          });
          vm.info = res.result;
          vm.pds = _pds;
        },
        error(e) {
          vm.$dialog.loading.close();
          vm.$dialog.toast({
            mes: "网络异常，请稍后重试！"
          });
        }
      });
    },
    goProducts(id) {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id }
      });
    },
    navigate(link) {
      if (!link) {
        this.$dialog.toast({
          mes: "数据对接中，敬请期待！"
        });
        return;
      }
      this.$router.push({ path: link });
    },
    goVocher() {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id: 1415 }
      });
    },
    getVersion() {
      let vm = this;
      plus.runtime.getProperty(plus.runtime.appid, function(inf) {
        vm.curVersion = inf.version;
        if (vm.type == "0") {
          vm.findUpgrade();
        }
      });
    },
    findUpgrade() {
      let vm = this;
      mui.ajax({
        url: findAppUpgredeByType,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          type: this.type,
          token: md5("gjfengfindAppUpgredeByType")
        },
        success(res) {
          let _result = res.result;
          if (_result.version && vm.curVersion < +_result.version) {
            vm.$store.commit("RECORD_UPDATE", true);
            vm.$dialog.confirm({
              title: `检测到新版本：${_result.version}，是否升级？`,
              mes: `${_result.describe}`,
              opts: () => {
                let wgtUrl = _result.jumpUrl;
                vm.downloadWgt(wgtUrl);
              }
            });
          }
        }
      });
    },
    downloadWgt(url) {
      let vm = this;
      this.$dialog.loading.open("下载更新...");
      plus.downloader
        .createDownload(url, { filename: "_doc/update/" }, function(d, status) {
          if (status == 200) {
            vm.installWgt(d.filename); // 安装更新包
          } else {
            vm.$dialog.alert({
              mes: "下载更新失败！"
            });
          }
          vm.$dialog.loading.close();
        })
        .start();
    },
    installWgt(path) {
      let vm = this;
      this.$dialog.loading.open("安装更新...");
      plus.runtime.install(
        path,
        {},
        function() {
          vm.$dialog.loading.close();
          vm.$dialog.alert({
            mes: "应用资源更新完成！",
            callback: () => {
              plus.runtime.restart();
            }
          });
        },
        function(e) {
          vm.$dialog.loading.close();
          vm.$dialog.alert({
            mes: "安装更新失败[" + e.code + "]：" + e.message
          });
        }
      );
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
section {
  margin-bottom: @pd / 2;
  background-color: @white;
}
.yd-slider-item img {
  max-height: 4rem;
}
.platform-container {
  padding: 0.2rem 0 0.1rem;
  .platform-item {
    margin-bottom: @pd;
    width: 45%;
    img {
      width: 100%;
    }
  }
}
.vocher-container {
  img {
    width: 100%;
  }
  .news-box {
    padding: @pd 0.1rem 0;
    .news-tag {
      display: inline-block;
      margin-right: 0.1rem;
      color: @red;
      border: 1px solid @red;
      border-radius: 10px;
      padding: 2px 5px;
      .text-center;
    }
    .news-title {
      .ellipsis;
    }
  }
}
.pd-list {
  ul {
    .pd-h;
  }
  .pd-item {
    width: 33%;
    padding: 0.1rem;
    margin-bottom: @pd;
    border: 1px solid #dfdfdf;
    .img-cover {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      img {
        width: 100%;
        .hv-cen;
      }
    }
    .title {
      height: 40px;
      line-height: 20px;
      h3 {
        .multi-ellipsis(2);
        font-size: 0.3rem;
        font-weight: normal;
        word-wrap: break-word;
        word-break: break-all;
        color: #333;
      }
    }
    .price {
      margin-top: 0.1rem;
      font-size: 14px;
      span:first-of-type {
        color: @red;
      }
      span:nth-of-type(2) {
        font-size: 12px;
        color: @lightgray;
      }
    }
  }
}
</style>