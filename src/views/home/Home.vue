<template>
    <div>
        <nav-bar class="home_nav"><div slot="center">购物街</div></nav-bar>
        <home-swiper :banners="banners"/>
        <home-recommend-view :recommends="recommends"/>
    </div>
</template>
<script>
    import NavBar from 'components/common/navbar/NavBar.vue'
    import {getHomeMultidata} from "network/home.js"
    import HomeSwiper from './childComponend/Home-Swiper.vue'
    import HomeRecommendView from './childComponend/HomeRecommendView.vue'
    


    export default {
        components: {
            NavBar,
            HomeSwiper,
            HomeRecommendView,
            
        },
        name:'Home',
        data(){
            return{
               banners:{},
               recommends:{}
            }
        },
        created(){
            getHomeMultidata().then(res=>{
                this.banners = res.data.banner.list;
                this.recommends = res.data.recommend.list;
            })
        }
    }
</script>
<style scoped>
    .home_nav{
        background: var(--color-tint);
        color: #fff;
        font-size: 18px;
    }
</style>
