<template>
    <div class="tab-bar-item" @click="itemClick">
        <div v-if="!isActive"><slot name="item-icon"></slot></div>
        <div v-else><slot name="item-icon-active"></slot></div>
        <div :style="activeStyle"><slot name="item-text"></slot></div>  
    </div>
</template>
<script>
    export default {
       name:'TabBar',
       props:{  //需要两个参数，判断此组件的路径和激活时文字颜色
           path:String,
           activeColor:{
               type:String,
               default:'#d81e06'
           }
       },
       data(){
           return{
               
           }
       },
       computed:{
           isActive(){
               return !this.$route.path.indexOf(this.path)
           },
           activeStyle(){
               let activeColor = this.activeColor
               if(activeColor==""||activeColor==null){  //判断传过来的颜色是否为空
                   activeColor='#d81e06'
               }
               return this.isActive ? {color:activeColor} : {}
           }
       },
       methods:{
           itemClick(){  //路由跳转
               this.$router.replace(this.path)
           }
       }
    }
</script>
<style scoped>
    .tab-bar-item {
        flex: 1;
        text-align: center;
        height: 49px;
        font-size: 14px;
    }

    .tab-bar-item img {
        width: 24px;
        height: 24px;
        margin-top: 3px;
        vertical-align: middle;
        margin-bottom: 2px;
    }
</style>
