---
layout: post
title: "小程序弹窗授权"
subtitle: "小程序蒙版弹窗授权"
date: 2019-07-21 12:00:00
author: "Li"
header-img: "img/post-bg-js-version.jpg"
header-mask: 0.4
tags:
  - JS
  - 前端
  - 小程序
  - 授权
---


---

最近在做一个小程序的复现，需要做一个弹窗授权的提示，由于小程序的官方API不支持弹窗授权，只能自己写一个蒙版弹窗了，

wxml
```html
<view class="modalDlg" wx:if="{% raw %}{{% endraw %}{!hasUserInfo && canIUse}}">
<view class='close_mask' bindtap="close_mask">X</view>
<image src="../../img/home_ic_college2.png" style="width:340rpx;height:340rpx"></image>
<p style="font-weight:bold">欢迎来到~</p>
<button id="login" wx:if="{% raw %}{{% endraw %}{!hasUserInfo && canIUse}}" open-type="getUserInfo" bindgetuserinfo="getUserInfo"> 微信授权 </button>
</view>
<view class="mask" catchtouchmove="preventTouchMove" wx:if="{% raw %}{{% endraw %}{!hasUserInfo && canIUse}}"></view>
```
index.js
```javascript
const app = getApp()

Page({
  data: {
    userInfo: {},
    hasUserInfo: false,
    canIUse: wx.canIUse('button.open-type.getUserInfo')
  },

  //事件处理函数
  bindViewTap: function() {
    wx.navigateTo({
      url: '../logs/logs'
    })
  },

  submit: function() {
    this.setData({
      showModal: true
    })
  },
  preventTouchMove: function() {},
  close_mask: function() {
    this.setData({
      showModal: false
    })
  },
  
  onLoad: function() {
    if (app.globalData.userInfo) {
      this.setData({
        userInfo: app.globalData.userInfo,
        hasUserInfo: true
      })

    } else if (this.data.canIUse) {
      // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
      // 所以此处加入 callback 以防止这种情况
      app.userInfoReadyCallback = res => {
        this.setData({
          userInfo: res.userInfo,
          hasUserInfo: true
        })
      }
    } else {
      // 在没有 open-type=getUserInfo 版本的兼容处理
      wx.getUserInfo({
        success: res => {
          app.globalData.userInfo = res.userInfo
          this.setData({
            userInfo: res.userInfo,
            hasUserInfo: true
          })

        }
      })
    }
  },
  getUserInfo: function(e) {
    console.log(e)
    app.globalData.userInfo = e.detail.userInfo
    this.setData({
      userInfo: e.detail.userInfo,
      hasUserInfo: true
    })
  }
  })
```

css文件
```css
    .mask {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background: #000;
  z-index: 9000;
  opacity: 0.7;
}
.modalDlg {
  width: 580rpx;
  height: 600rpx;
  position: fixed;
  top: 50%;
  left: 0;
  z-index: 9999;
  margin: -370rpx 85rpx;
  background-color: #fff;
  border-radius: 6rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
}
#login {
  color: #fff;
  width: 90%;
  height: 80rpx;
  line-height: 80rpx;
  background: #1AAD17;
  text-align: center;
  border-radius: 40rpx;
  margin-top: 44rpx;
  font-size: 28rpx;
  text-indent: 0em;
}
.close_mask {
  color: #000;
  position: relative;
  left: 40%;
  /* top: -82%; */
  font-size: 32rpx;
}
```
因为小程序异步的问题，并且getuserinfo是一个网络请求，且蒙版判断条件如下，

    <view class="modalDlg" wx:if="{% raw %}{{% endraw %}{!hasUserInfo && canIUse}}">


在已授权的情况下，重复进入小程序首页时，可能会闪出一次蒙版授权弹窗，后续建议给已授权的用户一个Flag来判断是否已授权，建议查阅授权的时效来决定采用哪种操作。