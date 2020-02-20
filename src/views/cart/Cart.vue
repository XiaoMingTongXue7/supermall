<template>
    <div>
        <nav-bar class="nav-bar">
            <div slot="center">购物车({{length}})</div>
        </nav-bar>

        <cart-list/>
        <cart-bottom-bar/>
    </div>
</template>

<script>
    import NavBar from "../../components/common/navbar/NavBar";
    import {mapGetters} from 'vuex';
    import CartList from "./childComps/CartList";
    import CartBottomBar from "./childComps/CartBottomBar";

    export default {
        name: "Cart",
        components: {CartBottomBar, CartList, NavBar},
        computed: {
            ...mapGetters({
                length: 'cartLength'
            })
        }
    }
</script>

<style scoped>
    .nav-bar{
        background-color: var(--color-high-text);
        color: #fff;
    }
</style>