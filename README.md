# selectAddress
仿京东选择地址(uni-app插件)
###使用方式
在 script 中引用组件
```
import selectAddress from '@/components/yixuan-selectAddress/yixuan-selectAddress.vue'
export default {
components: {selectAddress}
}
```
在 template 中使用组件
```
<button type="primary" @click="btnClick">选择地址</button>
<selectAddress ref='selectAddress' @selectAddress="successSelectAddress"></selectAddress>
```
```
export default {
methods: {
btnClick() {
this.$refs.selectAddress.show()
},
successSelectAddress(address){ //选择成功回调
console.log(address)
}
}
}
```
![IMG_0985.PNG](https://upload-images.jianshu.io/upload_images/3044645-2fac905579791357.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

