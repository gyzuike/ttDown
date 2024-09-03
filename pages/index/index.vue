<template>  
  <view class="container">  
    <view class="titleback">      
      <text class="appBack" @click="goBack"><<</text>     
      <view class="appTitle">👉 抖音鸡掰神器 👈</view>
    </view>
    <textarea v-model="inputText" placeholder="请输入抖音分享链接"></textarea>  
    <button @click="clearData" class="clsText">清空</button>  
    <button @click="parseLink">解析链接</button>  
    <view v-if="parsedData && parsedData.url">  
      <button @click="copyLink">复制链接</button>  
      <text v-if="isCopied" class="copySuccess">链接已复制，请前往浏览器下载</text>  
    </view>  
    <text v-if="isLoading" class="apiloading">加载中...</text>  
  </view>  
</template>  

<script>  
export default {  
  data() {  
    return {  
      inputText: '',  
      parsedData: null,  
      isLoading: false,  
      isCopied: false  
    };  
  },  
  methods: {  
    goBack() {
      uni.navigateBack({
        delta: 1 // 返回的页面数，如果 delta 大于现有页面数，则返回到首页
      });
    },
    parseLink() {  
      this.isLoading = true;  
      let link = this.inputText.match(/https?:\/\/[^\s]+/)?.[0];  
      if (link) {  
        this.fetchVideoData(link);  
      } else {  
        this.isLoading = false;  
        uni.showToast({ title: '未找到有效的链接', icon: 'none' });  
      }  
    },  
    fetchVideoData(url) {  
      const fullUrl = `https://api.shuyuzi.com/?url=${encodeURIComponent(url)}`;    
      uni.request({  
        url: fullUrl,  
        success: (res) => {  
          this.isLoading = false;  
          if (res.data.code === 200) {  
            this.parsedData = res.data.data;  
          } else {  
            uni.showToast({ title: '数据获取失败', icon: 'none' });  
          }  
        },  
        fail: () => {  
          this.isLoading = false;  
          uni.showToast({ title: '网络请求失败', icon: 'none' });  
        }  
      });  
    },  
    copyLink() {  
      if (this.parsedData && this.parsedData.url) {  
        uni.setClipboardData({  
          data: this.parsedData.url,  
          success: () => {  
            this.isCopied = true;  
            setTimeout(() => {  
              this.isCopied = false;  
            }, 5000);  
          },  
          fail: () => {  
            uni.showToast({ title: '复制失败', icon: 'none' });  
          }  
        });  
      }  
    },  
    clearData() {  
      this.inputText = '';  
      this.parsedData = null;  
      this.isLoading = false;  
      this.isCopied = false;  
    }  
  }  
};  
</script>  

<style>  
.container {  
  padding: 20px;  
  background-image: url(https://t.alcy.cc/mp/);  
  background-repeat: no-repeat;
  background-size: cover;
  height: 100vh;  
  color: #ff007b;  
  display: flex;  
  flex-direction: column;  
}  
textarea {  
  color: #ff007b;  
  margin-bottom: 20px;  
  padding: 5%;  
  background-color: #ffffff66;  
  width: 90%;  
  border: 1px solid #ffffff;  
}  
button {  
  margin-bottom: 10px;  
  border: 1px solid #ffffff;  
  color: #ff007b;  
  background-color: #ffffffcc;
  width: 100%;
}  
.copySuccess {  
  margin-top: 10px;  
  color: #ff007b;  
}  
.clsText {  
  width: 80px;  
  height: 50px;  
  margin-top: -80px;
  margin-bottom: 35px;
  margin-right: 10px
}  
.apiloading {  
  margin-top: 20px;  
}  
uni-textarea {
  width: 90% !important;
}
.uni-textarea-placeholder {  
  color: #f123c1 !important;  
}  
.titleback {
  display: flex;
  align-items: center;
  margin: 20px 0;
}
.appBack {
  margin-right: 10px; /* 调整返回图标的间距 */
  font-size: 24px; /* 调整图标大小 */
  color: #ff007b; /* 调整图标颜色 */
  cursor: pointer; /* 添加指针样式 */
}
.appTitle {
  flex-grow: 1; /* 使标题占据剩余空间 */
  text-align: center; /* 文字居中 */
  font-size: 1.2em;
  font-weight: 600;
}
</style>