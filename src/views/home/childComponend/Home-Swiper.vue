<template>
    <div>
        <swiper>
            <swiper-item v-for="item in banners">
                <a :href="item.link">
                    <img :src="item.image" @load="imageLoad"/>
                </a>
            </swiper-item>
        </swiper>
    </div>
</template>
<script>
    import {Swiper,SwiperItem} from "components/common/swiper"
    export default {
       name:'Home-Swiper',
       data(){
           return {
               isEmit : false
           }
       },
       props:{
           banners:{
               type:Array,
               default(){
                   return []
               }
           }
       },
       components:{
           Swiper,
            SwiperItem 
       },
       methods:{
           imageLoad(){
               //使用isEmit标记只发一次事件
               if(!this.isEmit){
                   //图片加载完成时想外发出事件
                    this.$emit("swiperImageLoad")
                    this.isEmit = true
               }
                
           }
       }
    }
</script>
<style scoped>

</style>
