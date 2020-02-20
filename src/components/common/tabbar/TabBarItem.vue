<template>
  <div class="tab-bar-item" @click="itemClick">
    <slot v-if="!isActive" name="item-icon"></slot>
    <slot v-else name="item-icon-active"></slot>
    <div :style="activeStyle"><slot name="item-text"></slot></div>
  </div>
</template>

<script>
    export default {
      name: "TabBarItem",
      props:{
        path:String,
        activeColor:{
          type:String,
          default:'red'
        }
      },
      data(){
          return{
            // isActive:true
          }
      },
      computed:{
        isActive(){
          return this.$route.path.indexOf(this.path) !== -1
        },
        activeStyle(){
          return this.isActive ? {color:this.activeColor} : {}
        }
      },
      methods:{
          itemClick(){
            //解决双击报错的问题
            if(this.$route.path !== this.path){this.$router.replace(this.path)}
            // this.$router.replace(this.path)
          }
      }
    }
</script>

<style scoped>
  .tab-bar-item{
    flex: 1;
    text-align: center;
    height: 49px;
    font-size: 13px;
    margin-top: 3px;
  }
  .tab-bar-item img{
    width: 20px;
    height: 20px;
    vertical-align: middle;
    margin-top: 3px;
  }
</style>
