<template>
  <view>
    <!-- 筛选 -->
    <view class="filter">
      <text class="active">综合</text>
      <text>销量</text>
      <text>价格</text>
    </view>
    <!-- 商品列表 -->
    <scroll-view class="goods" scroll-y @scrolltolower="pageAgain">
      <view class="item" 
         @click="goDetail(item.goods_id)" 
         v-for="item in list" 
         :key="item.goods_id">
        <!-- 商品图片 -->
        <image class="pic" :src="item.goods_small_logo"></image>
        <!-- 商品信息 -->
        <view class="meta">
          <view class="name">{{item.goods_name}}</view>
          <view class="price">
            <text>￥</text>{{item.goods_price}}<text>.00</text>
          </view>
        </view>
      </view>
    </scroll-view>
  </view>
</template>

<script>
  export default {
    data(){
      return{
        query:"",
        list:[],
        pagenum:1,
        flag:"请求结束"
      }
    },
    methods: {
      goDetail (id) {
        uni.navigateTo({
          url: '/pages/goods/index?id='+id
        })
      },
      async getList(query){
       const res = await this.request({
          url:"/api/public/v1/goods/search",
          data:{
            query,
            pagenum:this.pagenum,
            pagesize:5
          }
        })
        // console.log(res)
        // this.list = res.goods
        // 使用concat，加载新数据
        this.list = this.list.concat(res.goods)
      },
      async pageAgain(){
       if(this.flag == "请求中"){
         return;
       }
       this.flag="请求中"
      //  请求下一页
       this.pagenum++
       await this.getList(this.query)
       this.flag="请求完成"
      }
    },
    onLoad(e){
        // console.log(e.query)
        this.query = e.query
        // 调用
        this.getList(e.query)
     }
  }
</script>

<style scoped lang="less">
  .filter {
    display: flex;
    height: 96rpx;
    line-height: 96rpx;
    border-bottom: 1rpx solid #ddd;

    /* #ifdef H5 */
    position: relative;
    z-index: 99;
    /* #endif */

    text {
      flex: 1;
      text-align: center;
      font-size: 30rpx;
      color: #333;

      &.active {
        color: #ea4451;
      }
    }
  }
  
  .goods {
    position: absolute;
    width: 100%;
    top: 97rpx;
    bottom: 0;
  }

  .item {
    display: flex;
    padding: 30rpx 20rpx 30rpx 0;
    margin-left: 20rpx;
    border-bottom: 1rpx solid #eee;

    &:last-child {
      border-bottom: none;
    }

    .pic {
      width: 200rpx;
      height: 200rpx;
      margin-right: 30rpx;
    }

    .meta {
      flex: 1;
      font-size: 27rpx;
      color: #333;
      position: relative;
    }

    .name {
      width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
    }

    .price {
      position: absolute;
      bottom: 0;

      color: #ea4451;
      font-size: 33rpx;

      text {
        font-size: 22rpx;
      }
    }
  }
</style>
