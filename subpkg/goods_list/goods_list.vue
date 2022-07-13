<template>
  <view>
    <view class="goods-list">
      <!-- 不会被渲染 起到包裹、使结构更清晰的作用 -->
      <view v-for="(item,i) in goodsList" :key="i" @click="gotoDetail(item)">
        <my-goods :item="item"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          query: '',
          cid: '',
          pagenum: 1,
          pagesize: 10,
        },
        // 商品数据列表
        goodsList: [],
        // 总数量
        total: 0,
        // 节流阀 控制请求次数
        isLoading: false,
      };
    },
    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || '';
      console.log(this.queryObj);

      this.getGoodsList()
    },
    methods: {
      async getGoodsList(cb) {
        // 打开节流阀
        this.isLoading = true
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        // 关闭节流阀
        this.isLoading = false
        cb && cb() 
        if (res.meta.status !== 200) return uni.$showMsg()
        this.goodsList = [...this.goodsList , ...res.message.goods]
        this.total = res.message.total
      },
      gotoDetail(item){
        console.log(item)
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
        })
      }
    },
    onReachBottom(){
      // 判断数据是否请求完毕
      if(this.queryObj.pagenum*this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕')
      // 判断节流阀状态
      if(this.isLoading) return
      // 让页码值自增++
      this.queryObj.pagenum++;
      this.getGoodsList() 
    },
    onPullDownRefresh(){
      // 重置关键数据
      this.queryObj.pagenum = 1;
      this.total = 0;
      this.isLoading = false;
      this.goodsList = [];
      // 重新发送请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    },
  }
</script>

<style lang="scss">

</style>
