<template>
  <div>
    <header-top title="网上商城" :back="false" :login="!loginAccount">注册|登录</header-top>
    <main class='scroll-content'>
      <yd-pullrefresh  ref="homepage" :callback="refresh" :show-init-tip="false">
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
        <div class="flex align-center news-box" v-show="info.newList&&info.newList.result.length">
          <div class="rolltip"><span class="iconfont-large self-announce danger-color"></span></div>
          <yd-rollnotice autoplay="4000" class="flex-1">
            <yd-rollnotice-item v-for="(item,index) in info.newList&&info.newList.result" :key="index">
              <router-link :to="{name:'NewsDetail',query:{id:item.id}}" class="flex align-center" style="width:100%;">
                <span class="news-tag">{{item.source}}</span>
                <span class="news-title flex-1 fs-14">{{item.title}}</span>
              </router-link>
            </yd-rollnotice-item>
          </yd-rollnotice>
          <router-link :to="{name:'News',query:{query:true}}">更多></router-link>
        </div>
      </section>
      <section class="platform-container flex just-around">
        <div class="discount-tag">优惠购</div>
        <div v-for="(item,index) in platform" :key="index" class="platform-item" @click="navigate(item.link)">
          <img :src="getLocalImg(item.img)">
          <p>{{item.text}}</p>
        </div>
      </section>
      <section class="high-container">
          <img :src="getLocalImg('20180529_1.png')" alt="直降到底 嗨翻618">
        </section>
        <section class="hot-container">
          <div class="hot-box">
            <div class="hot-header">
              <img :src="getLocalImg('20180529_4.png')" alt="">
              <span>今日必抢</span>
            </div>
            <ul class="type-list clearfix">
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">必备厨具</div>
              </li>
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">家用电器</div>
              </li>
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">酒水副食</div>
              </li>
            </ul>
          </div>
          <template v-for="(item,index) in pds">
            <div :key="index" class="hot-box">
              <div class="hot-header">
                <img :src="getLocalImg('20180529_4.png')" alt="">
                <span>{{item.label}}</span>
              </div>
              <ul class="pd-list clearfix">
                <router-link :to="{path:'/online/product',query:{id:pd.id}}" v-for="pd in item.list.slice(0,3)" :key="pd.id" tag="li" class="pd-item fl">
                  <div class="img-cover">
                    <img :src="pd.imgUrl" :alt="pd.name">
                  </div>
                  <div class="title">
                    <h3>{{pd.name}}</h3>
                  </div>
                  <div class="price">
                    零售价:
                    <span class="danger-color fs-15">￥{{pd.price}}</span>
                  </div>
                </router-link>
              </ul>
            </div>
          </template>
        </section>
        <section class="activity-container clearfix">
          <div class="activity-header" :style="{'background-image':formatBg('20180529_2.png')}">
            <span class="jianbian-text1">爱生活</span>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">超值家电</h3>
              <p>美的大功率吸尘器</p>
              <span class="activity-desc jianbian-bg2">领券购物更优惠</span>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">精选厨具</h3>
              <p>美的大功率吸尘器</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">开胃饮料</h3>
              <p>SO有机苹果醋</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">男士理容</h3>
              <p>飞科全身水洗电动剃须刀</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
        </section>
        <section class="activity-container clearfix">
          <div class="activity-header" :style="{'background-image':formatBg('20180529_2.png')}">
            <span class="jianbian-text1">购好物</span>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">就爱高颜值</h3>
              <p>美的大功率吸尘器</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">美容护肤</h3>
              <p>白藜芦醇温润丰唇膏</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">大牌特价</h3>
              <p>TCL多功能家用电烤箱</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">送礼首选</h3>
              <p>贵州茅台天朝上品贵人酒</p>
              <span class="activity-desc jianbian-bg2">好物低价任你抢</span>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
        </section>
        <section class="merchant-container">
          <img :src="getLocalImg('20180529_3.jpg')" alt="开通跨行业盈利系统">
        </section>
      </yd-pullrefresh>
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
        { img: "taobao.jpg", link: "/online/tmindex", text: "淘宝" },
        { img: "tianmao.png", link: "/online/tmindex", text: "天猫" },
        { img: "jingdong.jpg", link: "/online/jdindex", text: "京东" },
        { img: "ziying.png", link: "/online/products?type=1&id=1398&update=true", text: "自营商城" },
        { img: "column.png", link: "/online/allcolumn", text: "所有分类" }
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
    this.checkLogin();
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
    checkLogin() {
      if (getStore("account") && getStore("account").length > 0) {
        this.loginAccount = true;
        this.$store.commit("SET_ACCOUNT", getStore("account"));
        if (!this.member) {
          this.$store.dispatch("getInfo");
        }
      } else {
        this.loginAccount = false;
      }
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
    refresh(){
      let pa = Promise.resolve(this.getInfo());
      let pb = Promise.resolve(this.checkLogin());
      Promise.all([pa,pb]).then((a,b)=>{
        this.$refs.homepage.$emit('ydui.pullrefresh.finishLoad');
      })
    },
    goProducts(id) {
      this.$router.push({
        name: "Products",
        query: { type: 1, id,update: true }
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
        query: { type: 1, id: 1415,update: true }
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
.yd-slider-item img {
  max-height: 4rem;
}
.platform-container {
   background-color: @white;
  padding: 0.5rem 0 0.1rem;
  position: relative;
  overflow: hidden;
  .discount-tag {
    position: absolute;
    left: -0.15rem;
    top: 0;
    border-radius: 10px;
    background-color: rgb(228, 1, 12);
    color: @white;
    font-size: 0.24rem;
    padding: 2px 0.1rem;
    width: 1rem;
    text-align: right;
  }
  .platform-item {
    margin-bottom: @pd;
    text-align: center;
    img {
      .wh(1rem,1rem);
      border-radius: 50%;
    }
    p {
      font-size: 0.26rem;
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
      margin: 0 0.1rem;
      color: @red;
      border: 1px solid @red;
      border-radius: 10px;
      padding: 1px 5px;
      .text-center;
    }
    .news-title {
      .ellipsis;
    }
  }
}
.high-container {
  background-color: @white;
  margin-top: 3px;
  img {
    width: 100%;
  }
}
.hot-container {
  padding-bottom: @pd;
  background-color: rgb(254, 30, 54);
  .hot-box {
    .hot-header {
      position: relative;
      color: @white;
      font-weight: bold;
      text-align: center;
      img {
        .wh(4rem,0.8rem);
      }

      span {
        .hv-cen;
        font-size: 0.32rem;
        z-index: 5;
      }
    }
  }
  .pd-item {
    width: 30.6%;
    margin-left: 2%;
    padding: 0.1rem;
    background-color: @white;
    .img-cover {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      background-color: @white;
      border-radius: 2px;
      img {
        width: 100%;
        .hv-cen;
        border-radius: 2px;
      }
    }
    .title {
      height: 40px;
      line-height: 20px;
      h3 {
        .multi-ellipsis(2);
        padding: 0 2px;
        font-size: 0.26rem;
        font-weight: normal;
        word-wrap: break-word;
        word-break: break-all;
        color: #333;
      }
    }
    .price {
      margin-top: 0.1rem;
      font-size: 0.2rem;
      text-align: right;
    }
  }
  .type-item {
    .type-title {
      color: @white;
      font-size: 0.28rem;
      font-weight: 700;
      text-align: center;
      border-radius: 0.4rem;
      margin: 0.2rem;
      padding: 3px 0;
    }
  }
}
.jianbian-bg1 {
  background: -webkit-linear-gradient(left, rgb(252, 10, 34), rgb(242, 134, 0));
}
.jianbian-bg2 {
  background: -webkit-linear-gradient(
    left,
    rgb(248, 44, 131),
    rgb(250, 92, 65)
  );
}
.jianbian-text1 {
  display: inline-block;
  background: -webkit-linear-gradient(left, rgb(73, 38, 180), rgb(7, 158, 227));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.activity-container {
  background-color: rgb(242, 242, 242);
  .activity-header {
    .pd-v;
    font-size: 18px;
    font-weight: bold;
    letter-spacing: 5px;
    text-align: center;
    background-size: 100%;
    background-repeat: no-repeat;
  }
  .activity-item {
    width: 50%;
    min-height: 2rem;
    padding: 10px 0;
    border-right: 1px solid #eee;
    border-bottom: 1px solid #eee;
    background-color: @white;
    box-shadow: 0 0 1px rgba(120, 120, 120, 0.3);
    &:nth-of-type(2n) {
      border-right: none;
    }
    .activity-text {
      width: 60%;
      padding-left: 8px;
      
      p {
        font-size: 10px;
        color: rgb(153, 153, 153);
        margin:5px 0;
      }
      .activity-desc {
        color: #fff;
        font-size: 8px;
        padding: 3px 5px;
        border-radius: 3px;
      }
    }
    .activity-thumb {
      width: 40%;
      img {
        width: 100%;
        padding-right: 8px;
      }
    }
    .activity-name {
      font-size: 16px;
    }
  }
}
.merchant-container {
  .pd;
  img {
    width: 100%;
  }
}
</style>