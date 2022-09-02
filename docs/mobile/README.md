# H5上传照片调用相册和拍照

```html
<input type="file" name="file" class="upload-file" @change="fileChange" ref="frontFile" accept="image/*"  capture="camera"/>
```

```js
//获取浏览器的userAgent,并转化为小写
var ua = navigator.userAgent.toLowerCase();
//判断是否是苹果手机，是则是true
var isIos = (ua.indexOf('iphone') != -1) || (ua.indexOf('ipad') != -1);

// var isWeixin = ua.match(/MicroMessenger/i)=="micromessenger";//微信判断
// var isQQInstalled = ua.indexOf(' qq')>-1 && ua.indexOf('mqqbrowser') < 0;//qq内置浏览器
var isQQ = ua.indexOf('mqqbrowser')> -1 && ua.indexOf(" qq")< 0;//qq浏览器

if (isQQ || isIos) {
  var files = document.querySelectorAll("input[type=file]");
  if(files) {
    files.forEach(item => {
      item.removeAttribute("capture")
    })
  }
}
```