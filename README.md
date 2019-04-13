# wechat-miniProgram
> ### record some methods
**目录**

|功能名称          |功能描述        |上传时间        |
| :-------------|:-------------|:-------------:|
|[1.自定义沉浸式头部导航组件设计](#1-自定义沉浸式头部导航组件设计)|自带返回，任意更改系统状态栏颜色的|2019-04-13|
|[2.全局按钮点击手机震动反馈](#2-全局按钮点击手机震动反馈)|监听页面内的点击元素，给自定义的按钮添加振动反馈|2019-04-13|



> #### 1 自定义沉浸式头部导航组件设计

* 创建名为`header-custom`的 `component` 组件,并将系统的状态栏改为沉浸式；
* 在`app.js`中初始化方法`onLaunch()`里面获取手机状态栏高度；
* 编写组件并`声明`自定义组件，然后在`app.json`中全局引用该组件；

    **app.json**
    ```javascript
    {
      "window": {
        "navigationBarBackgroundColor": "#39b54a",
        "navigationBarTitleText": "header",
        "navigationStyle": "custom",//沉浸式状态栏
        "navigationBarTextStyle": "white"
      },
      "usingComponents": {
        "header-custom":"/components/header-custom"//使用组件
      }
    }
    ```
    
    **app.js**
    ```javascript
    
    onLaunch: function() {
        wx.getSystemInfo({
          success: e => {
            this.globalData.StatusBar = e.statusBarHeight;
            let custom = wx.getMenuButtonBoundingClientRect();
            this.globalData.Custom = custom;  
            this.globalData.CustomBar = custom.bottom + custom.top - e.statusBarHeight;
          }
        })
      }
    ```
    
    **header-custom.wxml**
    ```html
    <view class="header-custom" style="height:{{CustomBar}}px">
      <view class="header-bar fixed {{bgImage!=''?'none-bg text-white bg-img':''}} {{bgColor}}" style="height:{{CustomBar}}px;padding-top:{{StatusBar}}px;{{bgImage?'background-image:url(' + bgImage+')':''}}">
        <view class="action" bindtap="BackPage" wx:if="{{isBack}}">
          <text class="icon-back"></text>
          <slot name="backText"></slot>
        </view>
        <view class="action border-custom"  wx:if="{{isCustom}}" style="width:{{Custom.width}}px;height:{{Custom.height}}px;margin-left:calc(750rpx - {{Custom.right}}px)">
          <text class="icon-back" bindtap="BackPage"></text>
          <text class="icon-homefill" bindtap="toHome"></text>
        </view>
        <view class="content" style="top:{{StatusBar}}px">
          <slot name="content"></slot>
        </view>
        <slot name="right"></slot>
      </view>
    </view>
    ```
    
    **header-custom.js**
    ```javascript
    const app = getApp();
    Component({
      /**
       * 组件的一些选项
       */
      options: {
        addGlobalClass: true,
        multipleSlots: true
      },
      /**
       * 组件的对外属性
       */
      properties: {
        bgColor: {
          type: String,
          default: ''
        }, 
        isCustom: {
          type: [Boolean, String],
          default: false
        },
        isBack: {
          type: [Boolean, String],
          default: false
        },
        bgImage: {
          type: String,
          default: ''
        },
      },
      /**
       * 组件的初始数据
       */
      data: {
        StatusBar: app.globalData.StatusBar,
        CustomBar: app.globalData.CustomBar,
        Custom: app.globalData.Custom
      },
      /**
       * 组件的方法列表
       */
      methods: {
        BackPage() {
          wx.navigateBack({
            delta: 1
          });
        },
        toHome(){
          wx.reLaunch({
            url: '/pages/index/index',
          })
        }
      }
    })
    ```
    **header-custom.wxss**
    ```css
    .header-custom {
      display: block;
      position: relative;
    }
    
    .header-custom .header-bar .content {
      width: calc(100% - 440rpx);
    }
    
    .header-custom .header-bar {
      min-height: 0px;
      padding-right: 200rpx;
      box-shadow: 0rpx 0rpx 0rpx;
      z-index: 9999;
    }
    
    .header-custom .header-bar .content image {
      height: 60rpx;
      width: 240rpx;
    }
    
    .header-custom .header-bar .border-custom {
      position: relative;
      background: rgba(0, 0, 0, 0.15);
      border-radius: 1000rpx;
      height: 30px;
    }
    
    .header-custom .header-bar .border-custom::after {
      content: " ";
      width: 200%;
      height: 200%;
      position: absolute;
      top: 0;
      left: 0;
      border-radius: inherit;
      transform: scale(0.5);
      transform-origin: 0 0;
      pointer-events: none;
      box-sizing: border-box;
      border: 1rpx solid #fff;
      opacity: 0.5;
    }
    
    .header-custom .header-bar .border-custom::before {
      content: " ";
      width: 1rpx;
      height: 110%;
      position: absolute;
      top: 22.5%;
      left: 0;
      right: 0;
      margin: auto;
      transform: scale(0.5);
      transform-origin: 0 0;
      pointer-events: none;
      box-sizing: border-box;
      opacity: 0.6;
      background-color: #fff;
    }
    
    .header-custom .header-bar .border-custom text {
      display: block;
      flex: 1;
      margin: auto !important;
      text-align: center;
      font-size: 34rpx;
    }  
    ```
    
    **header-custom.json**
    ```javascript
      {
        "component": true,//声明自定义组件
        "usingComponents": {}
      }
    ```
    


> #### 2 全局按钮点击手机震动反馈