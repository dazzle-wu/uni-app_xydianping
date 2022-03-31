<template>
  <view class="message">
    <ul>
      <li v-for="item in newsList" :key="item._id">
        <uni-card mode="style" :thumbnail="item.cover" @click="toContent(item._id)">
          {{ item.title }}
        </uni-card>
      </li>
    </ul>
  </view>
</template>

<script>
export default {
  data() {
    return {
      newsList: []
    }
  },
  methods: {
    getAllNews() {
      wx.cloud
        .callFunction({
          name: 'getAllNews'
        })
        .then(res => {
          this.newsList = res.result.data
        })
    },
    toContent(id) {
      wx.navigateTo({
        url: `/pages/message/news?id=${id}`
      })
    }
  },
  onLoad() {
    this.getAllNews()
  }
}
</script>

<style></style>
