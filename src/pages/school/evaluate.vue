// 添加评价
<template>
  <view class="evaluate">
    <uni-card mode="title" :title="business.name" :thumbnail="business.signboard">
      <view class="content">
        <view class="rate">
          <uni-rate class="overall" v-model="evaluateForm.overall" size="30" margin="20" />
        </view>
        <view v-if="evaluateForm.overall">
          <text class="score">{{ score[evaluateForm.overall] }}</text>
          <view class="rate" v-if="business.type === '餐饮'">
            <text class="taste">口味</text>
            <uni-rate v-model="evaluateForm.taste" size="20" margin="10" />
            <text class="score">{{ score[evaluateForm.taste] }}</text>
          </view>
          <view class="rate">
            <text class="environment">环境</text>
            <uni-rate v-model="evaluateForm.environment" size="20" margin="10" />
            <text class="score">{{ score[evaluateForm.environment] }}</text>
          </view>
          <view class="rate">
            <text class="service">服务</text>
            <uni-rate v-model="evaluateForm.service" size="20" margin="10" />
            <text class="score">{{ score[evaluateForm.service] }}</text>
          </view>
          <uni-easyinput type="textarea" autoHeight v-model="evaluateForm.comment" placeholder="请输入内容"></uni-easyinput>
          <uni-file-picker limit="3" title="添加照片" @select="add" @delete="remove" />
        </view>
      </view>
    </uni-card>
    <button class="post" :disabled="evaluateForm.taste === 0 || evaluateForm.environment === 0 || evaluateForm.service === 0" @click="post">发布评价</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      business: {
        signboard: '',
        name: '',
        type: ''
      },
      evaluateForm: {
        avatar: '',
        name: '',
        business: 0,
        overall: 0,
        environment: 0,
        service: 0,
        comment: '',
        images: []
      },
      score: ['', '很差', '较差', '一般', '满意', '超赞'],
      imageValue: []
    }
  },
  methods: {
    // 添加图片
    add(e) {
      this.imageValue.push(...e.tempFilePaths)
    },
    // 移除图片
    remove(e) {
      this.imageValue = this.imageValue.filter(item => item !== e.tempFilePath)
    },
    // 上传图片
    async upload() {
      const imgArr = []
      for (let i = 0; i < this.imageValue.length; i++) {
        await wx.cloud
          .uploadFile({
            cloudPath: `${Date.now()}.png`,
            filePath: this.imageValue[i]
          })
          .then(res => {
            imgArr[i] = res.fileID
          })
      }
      this.$set(this.evaluateForm, 'images', imgArr)
    },
    // 发布评价
    async post() {
      wx.showLoading({
        title: '发布中',
        mask: true
      })
      await this.upload()
      wx.cloud
        .callFunction({
          name: 'retrieveUser'
        })
        .then(res => {
          const { avatar = res.result.data[0].avatarUrl, name = res.result.data[0].nickName } = res.result.data[0]
          this.$set(this.evaluateForm, 'avatar', avatar)
          this.$set(this.evaluateForm, 'name', name)
          wx.cloud
            .callFunction({
              name: 'createComment',
              data: this.evaluateForm
            })
            .then(res => {
              wx.hideLoading()
              wx.reLaunch({
                url: `/pages/school/comment?id=${this.evaluateForm.business}`
              })
            })
        })
    }
  },
  onLoad(options) {
    this.$set(this.business, 'signboard', options.signboard)
    this.$set(this.business, 'name', options.name)
    this.$set(this.business, 'type', options.type)
    this.$set(this.evaluateForm, 'business', options.id)
    if (options.type === '餐饮') {
      this.$set(this.evaluateForm, 'taste', 0)
    }
  }
}
</script>

<style lang="scss">
.evaluate {
  uni-card {
    .uni-card__title-content {
      padding-left: 10px;
    }
    .content {
      text-align: center;
      .rate {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 40px;
        uni-rate {
          margin-left: 10px;
        }
        uni-rate.overall {
          margin-left: 20px;
        }
      }
      .score {
        display: inline-block;
        width: 56rpx;
        font-size: 24rpx;
        color: #ffca3e;
      }
      .uni-easyinput {
        padding: 24rpx 0;
      }
    }
  }
  .post {
    margin: 24rpx 30rpx;
    color: #fff;
    background-color: var(--themeColor);
  }
}
</style>
