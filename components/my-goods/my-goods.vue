<template>
  <view class="godds-item">
    <view class="goods-item-left">
      <image src="item.goods_small_logo ||defaultPic" class="goods-pic"></image>
    </view>
    <!-- 右侧 -->
    <view class="goods-item-right">
      <!-- 商品名字 -->
      <view class="goods-name">
        {{item.goods_name}}
      </view>
      <!-- 商品价格 -->
        <view class="goods-info-box">
          <view class="goods-price">
            ￥{{item.goods_price}}  
          </view>
        </view>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-goods",
    props:{
      item:{
        type:Object,
        default:{}
      }
    },
    data() {
      return {
        // 默认图片
        defaultPic:'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    }
  }
</script>

<style lang="scss">
.goods-list{
  .godds-item{
    display: flex;
    padding: 10px 5px;
    border-bottom: 1px solid #efefef;
    .goods-item-left{
      margin-right: 5px;
      .goods-pic{
        width: 100px;
        height: 100px;
        display: block;
      }
    }
    .goods-item-right{
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      .goods-name{
        font-size: 13px
      }
      .goods-info-box{
        .goods-price{
          color: #C00000;
          font-size: 16px
        }
      }
    }
  }
}
</style>