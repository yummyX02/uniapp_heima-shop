<template>
  <view v-if="goods_info.goods_name" class="goods-detail-container">
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in goods_info.pics" :key="i">
        <image :src="item.pics_big" @click="preview(i)"></image>
      </swiper-item>
    </swiper>
    <!-- 商品详情区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">
        ￥{{goods_info.goods_price}}
      </view>
      <!-- 商品信息主页 -->
      <view class="goods-info-body">
        <!-- 商品名字 -->
        <view class="goods-name">
          {{goods_info.goods_name}}
        </view>
        <!-- 收藏 -->
        <view class="favorite">
          <uni-icons type="star" size="18"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="fee">
        快递：免运费
      </view>
    </view>
    <rich-text :nodes="goods_info.goods_introduce"></rich-text>
    <!-- 商品导航区间区域 -->
    <view class="goods_nav">
      <uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick"
        @buttonClick="buttonClick" />
    </view>
  </view>

</template>

<script>
  export default {
    data() {
      return {
        //商品详情
        goods_info: {},
        options: [{
          icon: 'headphones',
          text: '客服'
        }, {
          icon: 'shop',
          text: '店铺',
          infoBackgroundColor: '#007aff',
          infoColor: "red"
        }, {
          icon: 'cart',
          text: '购物车',
          info: 2,
        }],
        buttonGroup: [{
            text: '加入购物车',
            backgroundColor: '#ff0000',
            color: '#fff'
          },
          {
            text: '立即购买',
            backgroundColor: '#ffa200',
            color: '#fff'
          }
        ]
      };
    },
    onLoad(options) {
      console.log(options);
      const goods_id = options['goods_id ']
      // const  {data:goods_id} = options;
      // const goods_id = JSON.stringify(options.goods_id)
      // 测试
      // console.log(options['goods_id ']);
      this.getGoodsDetail(goods_id)
    },
    methods: {
      // 定义请求商品详情数据的方法
      async getGoodsDetail(goods_id) {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/detail', {
          goods_id
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        // 为 data 中的数据赋值
        // 使用字符串的 replace() 方法，为 img 标签添加行内的 style 样式，从而解决图片底部空白间隙的问题
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
        this.goods_info = res.message
      },
      preview(i) {
        // 小程序提供的api用于进行图片预览
        uni.previewImage({
          current: i,
          // map用于循环数组中的图片 x.pics_big是返回的新数组 
          urls: this.goods_info.pics.map(x => x.pics_big)
        })
      },
      onClick(e){
        if(e.content.text === '购物车'){
          uni.switchTab({
            url:'/pages/cart/cart'
          })
        }
      }
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 750rpx;

    image {
      height: 100%;
      width: 100%;
    }
  }

  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    .price {
      color: #C00000;
      font-size: 18px;
      margin: 10px 0;
    }

    .goods-info-body {
      display: flex;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
        margin-right: 10px;
      }

      .favorite {
        width: 120px;
        font-size: 12px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        border-left: 1px solid #efefef;
        color: gray;
      }
    }

    .fee {
      font-size: 12px;
      color: gray;
      margin: 10px 0;
    }
  }

  .goods_nav {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }

  .goods-detail-container {
    padding-bottom: 50px;
  }
</style>
