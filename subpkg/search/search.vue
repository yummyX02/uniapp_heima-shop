<template>
  <view>
    <view class="search-box">
      <uni-search-bar @confirm="search" @input="input" :radius="100" cancelButton="none"></uni-search-bar>
    </view>
    <view class="suggest-list" v-if="searchResults.length!== 0">
      <view class="suggest-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
        <!-- 名字 -->
        <view class="goods-name">
          {{item.goods_name}}
        </view>
      <!-- 图标 -->
        <uni-icons type="forward" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-head">
        <text>搜索历史</text>
        <uni-icons type="trash-filled" size="16" @click="clean()"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-data">
        <uni-tag :text="item" v-for="(item,i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //延时器（避免连续输入、连续发送请求）
        timer: null,
        //搜索关键词
        kw: '',
        //搜索建议列表
        searchResults: [],
        //历史数据
        historyList:['123','a'],
      };
    },
    computed:{
      histories(){
        // 注意：由于数组是引用类型，所以不要直接基于原数组调用 reverse 方法，以免修改原数组中元素的顺序
            // 而是应该新建一个内存无关的数组，再进行 reverse 反转
            return [...this.historyList].reverse()
      }
    },
    onLoad(){
      this.historyList = JSON.parse(uni.getStorageSync('kw')||'[]')
    },
    methods: {
      clean() {
        this.historyList = [];
        uni.setStorageSync('kw','[]')
      },
      input(e) {
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          console.log(e);
         this.kw = e
          this.getSearchList()
        }, 500)
      },
      async getSearchList() {
        //判断搜索框是否为空
        if (this.kw === '') {
          this.searchResults = []
          return
        }
        const {
          data: res
        } = 
        await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        if (res.meta.status !== 200)
          return uni.$showMsg()
        this.searchResults = res.message
        this.saveSearchHistory()
      },
      gotoDetail(item){
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      saveSearchHistory(){
        // 保存历史
        // this.historyList.push(this.kw)//无法解决重复问题
        // 创建一个set实例对象
        const set = new Set(this.historyList)
        //因为添加会添加到最前面  所以得有删除再添加
        set.delete(this.kw)
        set.add(this.kw)
        this.historyList = Array.from(set)
        //将搜索数据永久存到本地
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      gotoGoodsList(kw){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query = ' +kw
        })
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }
  .suggest-list{
    padding: 0 5px;
    .suggest-item{
      border-bottom: 1px solid #efefef ;
      font-size: 12px;
      padding: 12px 0;
      display: flex;
      align-items: center;
      justify-content: space-between;
      .goods-name{
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
    }
  }
  .history-box{
    padding: 0 5px;
    .history-head{
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }
    .history-data{
      display: flex;
      flex-wrap: wrap;//允许换行
      .uni-tag{
        margin-top: 5px;
        margin-right: 5px;
      }
    }
  }
</style>
