<template>
    <div class="cart-list">
        <scroll class="content">
            <cart-list-item v-for="(item, index) in cartList" :product="item" :key="index"/>
        </scroll>
    </div>
</template>

<script>
    import {mapGetters} from 'vuex';
    import Scroll from "../../../components/common/scrooll/Scroll";
    import CartListItem from "./CartListItem";

    export default {
        name: "CartList",
        components: {CartListItem, Scroll},
        computed: {
            ...mapGetters(['cartList'])
        }
    }
</script>

<style scoped>
    .cart-list{
        height: calc(100vh - 49px - 49px);
    }

    .content{
        height: 100%;
        overflow: hidden;
    }
</style>