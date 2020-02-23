<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" :current-index="currentIndex"/>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages" ref="base"/>
      <detail-base-info :goods="goods"/>
      <detail-shop-info :shop="shop"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info ref="param" :param-info="paramInfo"/>
      <detail-comment-info ref="comment" :comment-info="commentInfo"/>
      <detail-recommend-info ref="recommend" :recommend-list="recommendList"/>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
  </div>
</template>

<script>
  import DetailNavBar from "./childComps/DetailNavBar";
  import {getDetail, Goods, Shop, GoodsParam, getRecommend} from "network/detail";
  import DetailSwiper from "./childComps/DetailSwiper";
  import DetailBaseInfo from "./childComps/DetailBaseInfo";
  import DetailShopInfo from "./childComps/DetailShopInfo";
  import Scroll from "components/common/scroll/Scroll";
  import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
  import DetailParamInfo from "./childComps/DetailParamInfo";
  import BackTop from "components/content/backTop/BackTop";
  import DetailCommentInfo from "./childComps/DetailCommentInfo";
  import DetailRecommendInfo from "./childComps/DetailRecommendInfo";

  export default {
    name: "Detail",
    components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      Scroll,
      BackTop,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      DetailRecommendInfo
    },
    data() {
      return {
        iid: null,
        topImages: [],
        goods: {},
        shop: {},
        detailInfo: {},
        paramInfo: {},
        isShowBackTop: false,
        // DsaveY:0,
        themeTops: [],
        currentIndex: 0,
        commentInfo: {},
        recommendList: []
      }
    },
    created() {
      //  1.保存转入的iid
      this.iid = this.$route.params.iid

      //  2.根据iid请求详情数据
      getDetail(this.iid).then(res => {
        // 1.获取顶部的图片轮播数据
        // console.log(res)
        const data = res.result
        this.topImages = data.itemInfo.topImages

        //  2.获取商品信息
        this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

        //  3.创建店铺信息对象
        this.shop = new Shop(data.shopInfo)

        //  4.保存商品的详情数据
        this.detailInfo = data.detailInfo

        //  5.获取参数信息
        this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

        // 6.保存评论信息
        if (data.rate.list) {
          this.commentInfo = data.rate.list[0]
        }
        // console.log(data)

        //  7.推荐

        getRecommend().then((res, error) => {
          if (error) return
          this.recommendList = res.data.list
        })
      })
    },
    updated() {
      // 获取需要的四个offsetTop
      this._getOffsetTops()
    },
    methods: {

      _getOffsetTops() {
        this.themeTops = []
        this.themeTops.push(this.$refs.base.$el.offsetTop)
        this.themeTops.push(this.$refs.param.$el.offsetTop)
        this.themeTops.push(this.$refs.comment.$el.offsetTop)
        this.themeTops.push(this.$refs.recommend.$el.offsetTop)
        // this.themeTops.push(Number.MAX_VALUE)
      },
      imageLoad() {
        this.$refs.scroll.refresh()
      },
      backClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      contentScroll(position) {
        // console.log(position);
        // this.DsaveY = this.$refs.scroll.getScrollY()
        this.isShowBackTop = (-position.y) > 1000

        // 2.监听滚动到哪一个主题
        this._listenScrollTheme(-position.y)
      },
      _listenScrollTheme(position) {
        let length = this.themeTops.length;
        for (let i = 0; i < length; i++) {
          let iPos = this.themeTops[i];
          if (position >= iPos && position < this.themeTops[i + 1]) {
            if (this.currentIndex !== i) {
              this.currentIndex = i;
            }
            break;
          }
        }
      },
      titleClick(index) {
        this.$refs.scroll.scrollTo(0, -this.themeTops[index], 100)
      }
    }
  }
</script>

<style scoped>
  #detail {
    position: relative;
    z-index: 9;
    background-color: white;
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    z-index: 9;
    background-color: white;
  }

  .content {
    height: calc(100% - 44px);
  }
</style>
