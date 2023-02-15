# 微信小程序开发使用security.imgSecCheck检查用户上传的图片

********

## 云函数

```js
const cloud = require('wx-server-sdk')

/**
 * by yuluo
 * 测试 成功
 */

cloud.init({
  env: cloud.DYNAMIC_CURRENT_ENV
})

// 图片检测云函数入口函数

exports.main = async (event, context) => {

  console.log("test")

  const wxContext = cloud.getWXContext()

  console.log("检测图片为：---->", event.fileName)

  console.log("图片检测的云函数执行开始……")
  try {
    const result = await cloud.openapi.security.imgSecCheck({
      media: {
        contentType: 'image/png',
        value: Buffer.from(event.fileName) // 这里必须要将小程序端传过来的进行Buffer转化,否则就会报错,接口异常
      }
      
    })
 
    //返回图片检测的结果到云开发的开发日志
    if (result && result.errCode === '87014') {
      console.log("图片检测的云函数执行结束……")
      return { code: 500, msg: '内容含有违法违规内容', data: result }
    } else {
      console.log("图片检测的云函数执行结束……")
      return { code: 200, msg: '内容ok', data: result }
    }
  } catch (err) {
    // 错误处理
   if (err.errCode === '87014') {
    console.log("图片检测的云函数执行结束……")
      return { code: 500, msg: '内容含有违法违规内容', data: err }
    }
    return { code: 502, msg: '调用imgSecCheck接口异常', data: err }
  }
}
```

## 前端调用

````js
console.log("图片检测开始……")
        //调用云函数
        wx.cloud.callFunction({
          name: 'quickstartFunctions',
          config: {
            env: this.data.envId
          },

          data: {
            type: 'photoPictureReview',
            fileName: filePath
          }
        }).then(res => {
          console.log("云函数调用的返回结果：", res);
          let {
            errCode
          } = res.result.data;
          switch (errCode) {
            case 87014:
              this.setData({
                resultText: '内容含有违法违规内容'
              })
              break;
            case 0:
              this.setData({
                resultText: '内容正常'
              })
              break;
            default:
              break;
          }
        })
````

## 路由

````js
const photoPictureReview = require('./photo/pictureReview')

exports.main = async (event, context) => {
	case 'photoPictureReview':
        return await photoPictureReview.main(event, context) 
   }
}
