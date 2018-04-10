# App Store 应用发布

## App Store 二维码扫描跳转出现：项目不可用，您要的产品目前在中国商店不提供。

问题原因：针对中国区的应用 URL 连接出现错误。

```
https://itunes.apple.com/app/1363855598
```

解决方法：

以上是错误的写法，需要在 App ID 值前面追加小写的 id。

```
https://itunes.apple.com/app/id1363855598
```
