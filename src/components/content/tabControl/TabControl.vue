<template>
  <div class="tab-control">
    <div v-for="(item,index) in titles" class="tab-control-item" :class="{active: index ===currentIndex}" @click="itemClick(index)">
      <span>{{item}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    name: "TabControl",
    props: {
      titles: {
        type: Array,
        default() {
          return []
        }
      }
    },
    data() {
      return {
        currentIndex: 0
      }
    },
    methods:{
      itemClick(index){
        this.currentIndex =index;
        this.$emit('tabClick',index)
      }
    }
  }
</script>

<style scoped>
  .tab-control {
    display: flex;
    text-align: center;
    font-size: 14px;
    height: 40px;
    line-height: 40px;
    background-color: white;
    /*box-shadow: 0 1px 3px rgba(50,50,50,.4);*/
  }

  .tab-control-item {
    flex: 1;
  }
  .tab-control-item span{
    padding: 5px 6px;
  }

  .active {
    color: var(--color-high-text);
  }

  .active span{
    border-bottom: 3px solid var(--color-high-text);
  }
</style>
