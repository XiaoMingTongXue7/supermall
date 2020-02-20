<template>
    <div id="detail">
        <detail-nav-bar class="detail-nav-bar" @titleClick="titleClick" ref="nav"/>
        <scroll class="scroll" ref="scroll" :probe-type="3" @scroll="contentScroll">
            <detail-swiper :top-images="topImages"/>
            <detail-base-info :goods="goods"/>
            <detail-shop-info :shop="shop"/>
            <detail-goods-info :detail-info="detailInfo" @imagesLoad="imagesLoad"/>
            <detail-param-info :param-info="paramInfo" ref="params"/>
            <detail-comment-info :comment-info="commentInfo" ref="comment"/>
            <goods-list :goods="recommends" ref="recommend"/>
        </scroll>
        <detail-bottom-bar @addToCart="addToCart"/>
        <back-top @click.native="backClick" v-show="isShowBackTop"/>
    </div>
</template>

<script>
    import DetailNavBar from "./childComps/DetailNavBar";
    import DetailSwiper from "./childComps/DetailSwiper";
    import DetailBaseInfo from "./childComps/DetailBaseInfo";
    import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
    import DetailShopInfo from "./childComps/DetailShopInfo";
    import DetailParamInfo from "./childComps/DetailParamsInfo";
    import DetailCommentInfo from "./childComps/DetailCommentInfo";

    import {getGoodDetail, Goods, Shop, GoodsParam, getRecommends} from "network/detail";
    import {itemListenerMixin, backTopMixin} from "../../common/mixin";

    import Scroll from "components/common/scrooll/Scroll";
    import GoodsList from "components/content/goods/GoodsList";
    import {debounce} from "../../common/util";
    import DetailBottomBar from "./childComps/DetailButtomBar";

    export default {
        name: "Detail",
        components: {
            DetailBottomBar,
            GoodsList,
            DetailCommentInfo,
            DetailParamInfo,
            DetailShopInfo,
            DetailBaseInfo,
            DetailSwiper,
            DetailNavBar,
            Scroll,
            DetailGoodsInfo
        },
        mixins: [itemListenerMixin, backTopMixin],
        data(){
            return{
                iid: null,
                topImages: [],
                goods: {},
                shop: {},
                detailInfo: {},
                paramInfo: {},
                commentInfo: {},
                recommends: [],
                themeTopYs: [],
                getThemeTopY: null,
                currentIndex: 0
            }
        },
        created() {
            this.iid = this.$route.params.iid;

            getGoodDetail(this.iid).then(res => {
                console.log(res);
                const data = res.result;
                this.topImages = data.itemInfo.topImages;
                this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services);
                this.shop = new Shop(data.shopInfo);
                this.detailInfo = data.detailInfo;
                this.paramInfo = new GoodsParam(data.itemParams);
                if(data.rate.cRate !== 0){
                    this.commentInfo = data.rate.list[0];
                }
            });

            getRecommends().then(res => {
                this.recommends = res.data.list;
            });

            this.getThemeTopY = debounce(() => {
                this.themeTopYs = [];
                this.themeTopYs.push(0);
                this.themeTopYs.push(this.$refs.params.$el.offsetTop);
                this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
                this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
                this.themeTopYs.push(Number.maxValue);
            })
        },
        methods: {
            imagesLoad(){
                this.newRefresh();
                this.getThemeTopY();
            },
            titleClick(index){
                this.$refs.scroll.scrollTo(0, -this.themeTopYs[index]+44);
            },
            contentScroll(position){
                const positionY = -position.y + 44;
                let length = this.themeTopYs.length;
                for(let i=0; i<length-1; i++){
                    if(i !== length && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]){
                        this.$refs.nav.currentIndex = this.currentIndex = i;
                    }
                }
                this.listenShowBackTop(position);
            },
            addToCart(){
                const product = {};
                product.image = this.topImages[0];
                product.title = this.goods.title;
                product.desc = this.goods.desc;
                product.price = this.goods.nowPrice;
                product.iid = this.iid;

                this.$store.dispatch('addCart', product).then(res => {
                    this.$toast.show(res);
                });
            }
        },
        destroyed() {
            this.$bus.$off('itemImageLoad', this.itemImgListener);
        }
    }
</script>

<style scoped>
    #detail{
        position: relative;
        z-index: 9;
        height: 100vh;
        background-color: #fff;
        overflow: hidden;
    }

    .detail-nav-bar{
        position: relative;
        z-index: 9;
        background-color: #fff;
    }

    .scroll{
        height: calc(100% - 44px - 49px);
    }
</style>