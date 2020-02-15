<template>
    <div id="detail">
        <detail-nav-bar class="detail-nav-bar"/>
        <scroll class="scroll" ref="scroll">
            <detail-swiper :top-images="topImages"/>
            <detail-base-info :goods="goods"/>
            <detail-shop-info :shop="shop"/>
            <detail-goods-info :detail-info="detailInfo" @imagesLoad="imagesLoad"/>
        </scroll>
    </div>
</template>

<script>
    import DetailNavBar from "./childComps/DetailNavBar";
    import DetailSwiper from "./childComps/DetailSwiper";
    import DetailBaseInfo from "./childComps/DetailBaseInfo";
    import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
    import DetailShopInfo from "./childComps/DetailShopInfo";

    import {getGoodDetail, Goods, Shop} from "network/detail";

    import Scroll from "components/common/scrooll/Scroll";

    export default {
        name: "Detail",
        components: {
            DetailShopInfo,
            DetailBaseInfo,
            DetailSwiper,
            DetailNavBar,
            Scroll,
            DetailGoodsInfo
        },
        data(){
            return{
                iid: null,
                topImages: [],
                goods: {},
                shop: {},
                detailInfo: {}
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
            });
        },
        methods: {
            imagesLoad(){
                this.$refs.scroll.refresh();
            }
        }
    }
</script>

<style scoped>
    #detail{
        position: relative;
        z-index: 9;
        height: 100vh;
        background-color: #fff;
    }

    .detail-nav-bar{
        position: relative;
        z-index: 9;
        background-color: #fff;
    }

    .scroll{
        height: calc(100% - 44px);
    }
</style>