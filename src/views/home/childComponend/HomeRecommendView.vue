<template>
    <div class="recommend">
        <div v-for="item in recommends" class="recommend-item">
            <a :href="item.link">
                <div><img :src="item.image"/></div>
                <span>{{item.title}}</span>
            </a>
        </div>
    </div>
</template>
<script>
    export default {
       name:'HomeRecommendView',
       props:{
           recommends:{
               type:Array,
               default(){
                   return []
               }
           }
       }
    }
</script>
<style scoped>
    .recommend{
        display: flex;
        width: 100%;

        padding: 10px 0 20px;
        border-bottom: 10px solid #eee;
    }
    .recommend-item{
        text-align: center;
        flex:1;
    }
    .recommend-item img{
        height: 80px;
        margin:5px;
    }
</style>
