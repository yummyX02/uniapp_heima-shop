<template>
  <view>
    <!-- 搜索组件 -->
    <my-search @click="gotoSearch()"></my-search>
    <view class="scroll-view-container">
      <!-- 左侧区域 -->
      <scroll-view scroll-y="true" :style="{height:wh +'px'}" class="left-scroll-view">
        <block v-for="(item,i) in cateList" :key="i">
          <view class="left-scroll-item" :class="['left-scroll-item',i===active?'active':'']" @click="activeChange(i)">
            {{item.cat_name}}
          </view>
        </block>

      </scroll-view>
      <!-- 右侧区域 -->
      <scroll-view class="right-scroll-view" scroll-y="true" :style="{height:wh +'px'}" :scroll-top="scrollTop">

        <view class="cate-lv2" v-for="(item2,i2) in cateList2" :key="i2">
          <!-- 二级分类的标题 -->
          <view class="cate-lv2-title">
            / {{item2.cat_name}} /
          </view>
          <!-- 三级分类的数据 -->
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- 三级分类的图片 -->
             <image :src="item3.cat_icon"></image> 
             <!-- 三级分类的文本 -->
             <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>

      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //当前设备可用屏幕高度
        wh: 0,
        //分类数据
        //一级分类
        cateList: [],
        active: 0,
        //二级分类
        cateList2: [],
        //三级分类
        cateList3: [],
        //动态滚动条的值
        scrollTop:0,
      };
    },

    onLoad() {
      const sysInfo = uni.getSystemInfoSync();
      // console.log(sysInfo)
      this.wh = sysInfo.windowHeight-50;
      //调用方法获取分类数据
      this.getCateList();

    },
    methods: {
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        if (res.meta.status !== 200) return uni.$showMsg();
        this.cateList = res.message;
        //为二级分类赋值
        this.cateList2 = res.message[0].children
      },
      activeChange(i) {
        this.active = i;
        console.log(this.active)
        //动态为二级分类赋值
        this.cateList2 = this.cateList[i].children
        this.scrollTop = this.scrollTop === 0? 1:0;
      },
      gotoGoodsList(item3){
        uni.navigateTo({
          //跳转页面函数
          url:'/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      },
      gotoSearch(){
        uni.navigateTo({
          url:'/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;
  }

  .left-scroll-view {
    width: 120px;

    .left-scroll-item {
      background-color: #F7F7F7;
      line-height: 60px;
      font-size: 12px;
      text-align: center;

      &.active {
        background-color: #FFFFFF;
        position: relative;

        &::before {
          display: block;
          content: '';
          width: 3px;
          height: 30px;
          top: 50%;
          left: 0;
          transform: translateY(-50%); //在Y轴上向负方向收缩50%
          background-color: #c00000;
          position: absolute;
        }
      }
    }
  }

  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }
  .cate-lv3-list{
    display: flex;
    flex-wrap: wrap;
    .cate-lv3-item{
      margin-bottom: 10px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      width: 33.33%;
      image{
        width: 60px;
        height: 60px;
      }
      text{
        font-size: 12px;
      }
    }
  }
</style>
