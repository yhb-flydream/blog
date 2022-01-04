# 小程序 A 页面有 cover-view 设置 border-radius，当跳转的 B 页面有 input type 为 number，当点击 input 调出数字键盘时会遮盖整个页面（Android 手机）

原因：未知

解决：

给 B 页面添加

```html
<cover-view class="cover-view"></cover-view>
```

```css
.cover-view {
  height: 1rpx;
  border-radius: 1px;
  border: 1px solid rgba(0, 0, 0, 0.01);
}
```
