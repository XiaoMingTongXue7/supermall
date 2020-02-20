<template>
    <div class="toast" v-show="isShow">
        {{message}}
    </div>
</template>

<script>
    export default {
        name: "Toast",
        data(){
            return{
                isShow: false,
                message: ''
            }
        },
        methods: {
            show(message='默认信息', time=1500){
                this.isShow = true;
                this.message = message;

                setTimeout(() => {
                    this.isShow = false;
                    this.message = '';
                }, time)
            }
        }
    }
</script>

<style scoped>
    .toast{
        position: fixed;
        z-index: 999;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: rgba(0, 0, 0, 0.6);
        color: #fff;
        padding: 8px 10px;
        border-radius: 13px;
    }
</style>