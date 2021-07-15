<template>
    <div id="home">
        <nav-bar class="home_nav"><div slot="center">购物街</div></nav-bar>
        <tab-control :titles="['流行','精选','推荐']"
                            class="tab-control"
                            ref="tabControlCopy"
                            @tabclick="tabClick"
                            v-show="isDisplay"/>
        <scroll class="content" ref="scroll"
            :probeType="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore">
            <home-swiper :banners="banners"
                @swiperImageLoad="swiperImageLoad"/>
            <home-recommend-view :recommends="recommends"/>
            <feature-view/>
            <tab-control :titles="['流行','精选','推荐']"
                            ref="tabControl"
                            @tabclick="tabClick"/>
            <goods-list :goods="goodsList"/>
        </scroll>
        <back-top @click.native="backClick" v-show="isShow"/>
    </div>
</template>
<script>
    import NavBar from 'components/common/navbar/NavBar.vue'
    import {getHomeMultidata,getHomeGoods} from "network/home.js"
    import HomeSwiper from './childComponend/Home-Swiper.vue'
    import HomeRecommendView from './childComponend/HomeRecommendView.vue'
    import FeatureView from './childComponend/FeatureView.vue'
    import TabControl from 'components/content/tabControl/TabControl.vue'
    import GoodsList from 'components/content/goods/GoodsList.vue'
    import Scroll from 'components/common/scroll/Scroll.vue'
    import BackTop from 'components/content/backTop/BackTop.vue'
    


    export default {
        components: {
            NavBar,
            HomeSwiper,
            HomeRecommendView,
            FeatureView,
            TabControl,
            GoodsList,
            Scroll,
            BackTop,
        },
        name:'Home',
        data(){
            return{
               banners:[],  //从后台获取轮播图数据
               recommends:[],
               //从后台获取的商品数据
               goods:{      
                   pop:{page:0,list:[]},
                   new:{page:0,list:[]},
                   sell:{page:0,list:[]},
               },
               currentType:'pop',   //当前浏览的分类
               isShow:false,        //是否显示回到顶部按钮
               tabOffsetTop:0,      //tabcontrol距顶部位置
               isDisplay: false,    //顶部tabcontrol是否显示
               saveY:0, //保存离开时页面位置
            }
        },
        created(){
            //网络请求多个数据
            this.getHomeMultidata()
            //请求商品数据
            this.getHomeGoods("pop")
            this.getHomeGoods("new")
            this.getHomeGoods("sell")
        },
        mounted(){
            //监听item中图片加载完成,使用数据总线
            const refresh = this.debounce(this.$refs.scroll.refresh,50)
            this.$bus.$on('itemImageLoad',()=>{
                refresh()
                
            })
        },
        activated(){
            this.$refs.scroll.refresh()
            this.$refs.scroll.scrollTo(0,this.saveY,0)  //回来时滚动到相应的位置
            
        },
        deactivated(){  
            this.saveY = this.$refs.scroll.getScrollY()  //离开时保存页面位置
        },
        computed:{
            goodsList(){
                return this.goods[this.currentType].list
            }
        },
        methods:{
            //防抖函数
            debounce(func,delay){  
                let timer = null;
                return function(...args){
                    //后一张图片如果在50ms之内到来，会把timer顶掉，在等500ms才会执行
                    //后一张图片如果在50ms之后到来，会进行刷新
                    if(timer) clearTimeout(timer)
                    timer = setTimeout(()=>{
                        func.apply(this,args)
                        },delay
                    )
                }
            },
            //监听点击事件
            tabClick(index){
                switch(index){
                    case 0:this.currentType = 'pop'
                        break
                    case 1:this.currentType = 'new'
                        break
                    case 2:this.currentType = 'sell'
                      break
                }
                this.$refs.tabControl.currentIndex = index;
                this.$refs.tabControlCopy.currentIndex = index;
            },
            backClick(){
                this.$refs.scroll.scrollTo(0,0,500) //回到0 ， 0 用500ms
            },
            contentScroll(position){
                if(position.y>-1000) this.isShow = false
                else this.isShow = true
                this.isDisplay = (-position.y) > this.tabOffsetTop
            },
            loadMore(){
                this.getHomeGoods(this.currentType)
            },
            //通过事件在轮播图图片加载完成时判断TabControl距顶部高度
            swiperImageLoad(){
                this.tabOffsetTop = this.$refs.tabControl.$el.offsetTop
            },

            //网络请求
            getHomeMultidata(){
                getHomeMultidata().then(res=>{
                    this.banners = res.data.banner.list;
                    this.recommends = res.data.recommend.list;
                })
            },
            getHomeGoods(type){
                const page = this.goods[type].page+1
                getHomeGoods(type,page).then(res => {
                    //把数组res.data.list的内容依次加到goods[type].list里
                    this.goods[type].list.push(...res.data.list)
                    this.goods[type].page++
                    this.$refs.scroll.finishPullUp()//完成滚动的PullUp，否者PullUp()只能执行一次
                })
            }
        }
    }
</script>
<style scoped>
    .home_nav{
        background: var(--color-tint);
        color: #fff;
        font-size: 18px;
        position: fixed;
        left: 0;
        right: 0;
        top: 0;
        z-index: 10;
    }
    #home{
        padding-top:44px;
        height: 100vh;
    }
    .tab-control {
        position: relative;
        z-index: 9;
    }

  .content {
    overflow: hidden;

    overflow: scroll;
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }
</style>
