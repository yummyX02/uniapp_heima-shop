<template>
  <view class="my_search-container" :style="{'background-color':bgcolor}" @click="searchBoxHandler()">
    <view class="my_search-box" :style="{'border-radius':radius+'px'}">
      <!-- 使用uniapp提供的组件 -->
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my_search",
    props: {
      bgcolor: {
        type: String,
        default: '#C00000',
      },
      radius:{
        type:Number,
        //px
        default:18
      }
    },
    data() {
      return {
        
        }
    },
    methods:{
      searchBoxHandler(){
        // console.log('search');
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
.my_search-container{
  height: 50px;
  // background-color: #c00000;
  display: flex;
  align-items: center;
  padding: 0 10px;
  .my_search-box{
    height: 36px;
    background-color: #FFFFFF;
    // border-radius: 18px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    .placeholder{
      font-size: 15px;
      margin-left: 5px;
    }
  }
}
</style>