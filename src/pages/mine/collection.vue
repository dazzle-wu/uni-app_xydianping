<template>
  <view class="collection">
    <ul>
      <li v-for="item in collection" :key="item._id">
        <uni-card is-full="true" @click="toComment(item._id)">
          <view class="content">
            <image class="thumbnail" mode="aspectFill" :src="item.signboard[0]"></image>
            <view class="text">
              <text class="name">{{ item.name }}</text>
              <text class="address">{{ item.address }}</text>
              <uni-tag size="small" type="primary" inverted="true" :text="item.type"></uni-tag>
            </view>
          </view>
        </uni-card>
      </li>
    </ul>
  </view>
</template>

<script>
export default {
  data() {
    return {
      collection: []
    }
  },
  methods: {
    toComment(id) {
      wx.navigateTo({
        url: `/pages/school/comment?id=${id}`
      })
    },
  },
  onLoad() {
    const collects = wx.getStorageSync('collects')
    collects.forEach(item => {
      wx.cloud
        .callFunction({
          name: 'getBusiness',
          data: {
            id: item
          }
        })
        .then(res => {
          this.collection.push(res.result.data)
        })
    })
  }
}
</script>

<style lang="scss">
.collection {
  uni-card {
    .content {
      display: flex;
      .thumbnail {
        width: 288rpx;
        height: 162rpx;
        border-radius: 10rpx;
      }
      .text {
        flex: 1;
        width: 0;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding-left: 24rpx;
        .name {
          font-size: 32rpx;
          text-overflow: ellipsis;
	        white-space: nowrap;
	        overflow: hidden;
        }
        .address {
          color: #666;
          text-overflow: ellipsis;
	        white-space: nowrap;
	        overflow: hidden;
        }
      }
    }
  }
}
</style>
