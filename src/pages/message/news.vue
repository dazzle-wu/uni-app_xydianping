<template>
  <view class="news">
    <image class="cover" :src="news.cover" mode="aspectFill"></image>
    <view class="text">
      <uni-title type="h1" :title="news.title"></uni-title>
      <view class="info">
        <uni-tag :text="news.type" size="small"></uni-tag>
        <uni-dateformat :date="news.date" :threshold="[60000, 604800000]" format="yyyy-MM-dd"></uni-dateformat>
      </view>
      <rich-text :nodes="news.content" class="content"></rich-text>
    </view>
  </view>
</template>

<script>
export default {
  data () {
    return {
      news: {}
    }
  },
  methods: {
    getNews(id) {
      wx.cloud.callFunction({
        name: 'getNews',
        data: {
          id
        }
      }).then(res => {
        this.news = res.result.data
      })
    }
  },
  onLoad(options) {
    this.getNews(options.id)
  }
}
</script>

<style lang="scss">
.news {
  .cover {
    width: 750rpx;
    height: 422rpx;
  }
  .text {
    padding: 20rpx;
    .info {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 20rpx 10rpx;
    }
    .content {
      text-align: justify;
    }
  }
}
</style>
