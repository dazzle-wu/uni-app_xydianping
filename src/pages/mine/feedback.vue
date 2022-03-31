// 建议反馈
<template>
  <view class="feedback">
    <uni-forms label-position="top" ref="feedbackRef" :modelValue="feedbackForm" :rules="feedbackRule">
      <uni-forms-item name="type" label="类型">
        <uni-data-picker v-model="feedbackForm.type" :localdata="types"></uni-data-picker>
      </uni-forms-item>
      <uni-forms-item name="detail" label="描述">
        <uni-easyinput type="textarea" autoHeight v-model="feedbackForm.detail"></uni-easyinput>
      </uni-forms-item>
      <uni-forms-item name="image" label="截图">
        <uni-file-picker limit="3" @select="add" @delete="remove" />
      </uni-forms-item>
    </uni-forms>
    <button class="submit" @click="submit">提交</button>
  </view>
</template>

<script>
export default {
  data () {
    return {
      feedbackForm: {
        type: '',
        detail: '',
        image: []
      },
      feedbackRule: {
        type: {
          rules: [
            {
              required: true,
              errorMessage: '请选择反馈类型'
            }
          ]
        },
        detail: {
          rules: [
            {
              required: true,
              errorMessage: '请输入反馈描述'
            }
          ]
        }
      },
      types: [
        { text: '功能建议', value: '功能建议' },
        { text: '问题反馈', value: '问题反馈' }
      ]
    }
  },
  methods: {
    // 添加截图
    add(e) {
      this.feedbackForm.image.push(...e.tempFilePaths)
    },
    // 移除截图
    remove(e) {
      this.feedbackForm.image = this.feedbackForm.image.filter(item => item !== e.tempFilePath)
    },
    // 上传截图
    async upload() {
      const imgArr = []
      for (let i = 0; i < this.feedbackForm.image.length; i++) {
        await wx.cloud
          .uploadFile({
            cloudPath: `${Date.now()}.png`,
            filePath: this.feedbackForm.image[i]
          })
          .then(res => {
            imgArr[i] = res.fileID
          })
      }
      this.$set(this.feedbackForm, 'image', imgArr)
    },
    // 提交
    submit() {
      this.$refs.feedbackRef
        .validate()
        .then(async res => {
          wx.showLoading({
            title: '提交中',
            mask: true
          })
          await this.upload()
          wx.cloud
            .callFunction({
              name: 'createFeedback',
              data: this.feedbackForm
            })
            .then(res => {
              wx.hideLoading()
              wx.reLaunch({
                url: '/pages/mine/mine'
              })
            })
        })
        .catch(err => {})
    }
  }
}
</script>

<style lang="scss">
.feedback {
  padding: 100rpx;
  .submit {
    background-color: var(--themeColor);
    color: #fff;
  }
}
</style>