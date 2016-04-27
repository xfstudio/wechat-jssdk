# wechat-jssdk
WeChat/WeiXin JS-SDK integration with NodeJS 

###Usage
`npm install wechat-jssdk`  
```
var wx = require('wechat-jssdk');
wx.initialize(wechatConfig);
```

Required `wechatConfig` info:  
```
{
  "wechatRedirectHost": "http://127.0.0.1",
  "wechatToken": "",
  "appId": "",
  "appSecret": "",
}
```

For other url configuration, there are default values, you can checkout the `./lib/config.js`.
  
###Setup your Wechat ENV  
1.Set your URL and Token in [Wechat Website](https://mp.weixin.qq.com)  
  you should provide a api(e.g `/api/wechat`) to let wechat verify your validation, 
  in your router: 
  ```
  var wx = require('wechat-jssdk');
  wx.initialize(wechatConfig);
  router.get('/api/wechat', function(req, res){
    if(wx.jssdk.verifySignature(req.query)) {
      res.send(req.query.echostr);
      return;
    }
    res.send("error");
  });
  ```
  if everything is OK, wechat will save your settings :).

2.You should also provide a api for your browser to get token for the current url  
  ```
  router.get('/get-signature', function(req, res) {
    wx.jssdk.getSignatureByURL(req.query.url, function(signatureDate) {
      res.json(signatureDate);
    });
  });
  ```
3.Now you can send the wx request in your browser to pass the verification.

###APIs
see [API wiki](https://github.com/JasonBoy/wechat-jssdk/wiki/API)

###Client Side
`var wechat = require('wechat-jssdk/client')` in your client side js, or anyother way you like to include this.  
`var wechatObj = wechat(config[, successCallback[, errorCallback[, debug[, customWechatJSUrl]]]])`  
or  
`var wechatObj = new wechat(config, ...)`  
where config will be: 

```javascript
var config = {
  //must has properties to finish the wechat signature verification
  'appId': 'xxx',
  'nonceStr': 'xxx', //the four properties below should be received like api '/get-signature' above
  'signature': 'xxx',
  'timestamp': 'xxx',
  'url': 'url',
  //below are optional
  'success': function(){}, //invoked if wechat signature sign succeeds, same as successCallback
  'error': function(err){}, //invoked if sign failed, same as errorCallback
  'debug': true, //enable debug mode, same as debug, property in `config` object has higher priority
  'jsApiList': [] //optional, pass all the jsapi you want, the default will be ['onMenuShareTimeline', 'onMenuShareAppMessage']
}
```
after signature signed, you can customize the share information:  
```javascript
//customize share on chat info
wechatObj.setChatConfig({
  title: 'title',
  link: location.href,
  imgUrl: '/logo.png',
  desc: 'description'
});
//customize share on timeline info
wechatObj.setMomentConfig({
  title: 'title',
  link: location.href,
  imgUrl: '/logo.png'
});
```
You can also access the original wechat object `wx` from `window.wx` or from `wechatObj.wx` to call other apis.  
Also you can update the sign config if it fails, pass the new must has configs to `wechatObj.updateConfig(newSignatureConfig)`, and then call `wechatObj.signSignature()` to resign the signature.

### LICENSE

MIT
