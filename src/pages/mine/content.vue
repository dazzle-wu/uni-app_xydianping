<template>
  <view class="content">
    <uni-forms label-position="top" :modelValue="feedback">
      <uni-forms-item name="type" label="类型">
        <uni-easyinput disabled v-model="feedback.type"></uni-easyinput>
      </uni-forms-item>
      <uni-forms-item name="detail" label="描述">
        <uni-easyinput type="textarea" autoHeight disabled v-model="feedback.detail"></uni-easyinput>
      </uni-forms-item>
      <uni-forms-item name="image" label="截图" v-if="feedback.image.length">
        <image mode="aspectFill" :src="item" v-for="item in feedback.image" :key="item" @click="previewImage(item, feedback.image)"></image>
      </uni-forms-item>
    </uni-forms>
    <uni-forms label-position="top" :modelValue="reply" v-if="reply._id">
      <uni-forms-item name="reply" label="回复">
        <uni-easyinput type="textarea" autoHeight disabled v-model="reply.reply"></uni-easyinput>
      </uni-forms-item>
    </uni-forms>
  </view>
</template>

<script>
export default {
  data() {
    return {
      feedback: {
        image: []
      },
      reply: {}
    }
  },
  methods: {
    getFeedback(id) {
      wx.cloud.callFunction({
        name: 'getFeedback',
        data: {
          id
        }
      }).then(res => {
        this.feedback = res.result.data
        if (this.feedback.status === 1) {
          this.getReply(id)
        }
      })
    },
    getReply(feedback) {
      wx.cloud.callFunction({
        name: 'getReply',
        data: {
          feedback
        }
      }).then(res => {
        this.reply = res.result.data[0]
      })
    },
    // 预览图片
    previewImage(current, urls) {
      wx.previewImage({
        current,
        urls
      })
    },
  },
  onLoad(options) {
    this.getFeedback(options.id)
  }
}
</script>

<style lang="scss">
.content {
  padding: 30rpx;
  image {
    display: inline-block;
    width: 200rpx;
    height: 200rpx;
    margin-right: 45rpx;
  }
  image:nth-child(3) {
    margin-right: 0;
  }
}
</style>
