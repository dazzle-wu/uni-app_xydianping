<template>
  <view class="reply">
    <uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="button" activeColor="#007aff"></uni-segmented-control>
    <view class="content">
      <view v-show="current === 0">
        <ul>
          <li v-for="item in noreply" :key="item._id">
            <uni-card :title="item.type" :extra="item.date | dateFormat" @click="toContent(item._id)">{{ item.detail }}</uni-card>
          </li>
        </ul>
      </view>
      <view v-show="current === 1">
        <ul>
          <li v-for="item in replied" :key="item._id">
            <uni-card :title="item.type" :extra="item.date | dateFormat" @click="toContent(item._id)">{{ item.detail }}</uni-card>
          </li>
        </ul>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      current: 0,
      items: ['待回复', '已回复'],
      noreply: [],
      replied: []
    }
  },
  methods: {
    onClickItem(e) {
      this.current = e.currentIndex
    },
    getMyFeedback() {
      wx.cloud
        .callFunction({
          name: 'getMyFeedback'
        })
        .then(res => {
          this.noreply = res.result.data.filter(item => item.status === 0)
          this.replied = res.result.data.filter(item => item.status === 1)
        })
    },
    toContent(id) {
      wx.navigateTo({
        url: `/pages/mine/content?id=${id}`
      })
    }
  },
  onLoad() {
    this.getMyFeedback()
  }
}
</script>

<style lang="scss">
.reply {
  .segmented-control {
    margin: 30rpx;
  }
  .uni-card__content {
    text-overflow: ellipsis;
	  white-space: nowrap;
	  overflow: hidden;
  }
}
</style>
