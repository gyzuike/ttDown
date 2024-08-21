<template>  
  <view class="container">  
	<text class="appTitle">👉 鸡掰神器 👈</text>
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
      isCopied: false  // 新增变量来控制复制成功的提示  
    };  
  },  
  methods: {  
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
            this.isCopied = true;  // 修改这个变量来显示复制成功的提示  
            // 这里可以设置一个定时器来自动隐藏提示，例如3秒后  
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
      this.isCopied = false;  // 清空时也隐藏复制成功的提示  
    }  
  }  
};  
</script>  
  
<style>  
.container {  
  padding: 20px;  
  background-color: #00947e;  
  height: 100vh;  
  color: #fff;  
  display: flex;  
  flex-direction: column;  
}  
textarea {  
  color: #fff;  
  margin-bottom: 20px;  
  padding: 5%;  
  background-color: #00947e;  
  width: 90%;  
  border: 1px solid #ffffff;  
}  
button {  
  margin-bottom: 10px;  
  border: 1px solid #ffffff;  
  color: #fff;  
  background-color: #00947e;
  width: 100%;
}  
.copySuccess {  
  margin-top: 10px;  
  color: #fff;  
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
  color: #d8d8cc !important;  
}  
.appTitle {
	margin-top: 1em;
	font-size: 1.2em;
	text-align: center;
	margin-bottom: 1em;
}
</style>