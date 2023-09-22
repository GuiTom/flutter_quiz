# 第3节：图片优化

像Image.assets(),Image.network()或者decorationImage这样的图片widget,底层解码调用了硬件解码，所以解码效率比较高，在解码速度方面可以优化的空间也很小。


### 图片的性能优化可以从以下两个方面入手:

##### 1.图片的本地缓存。请求过的图片，缓存在磁盘里面，在有效期之内，都不用再从网络请求了。cached_network_image就是做这个事情的。

cached_network_image 如果不设置memCachedWidth和memCachedHeight,是不对网络图片做本地压缩的。反之，会按照设置的缓存宽高压缩裁剪图片并缓存起来。需要提到一点是，该组件不支持按照BoxFit.cover模式对图片原像素进行裁剪，可以对原像素进行BoxFit.fitwidth(只设置memCachedWidth时)和BoxFit.fitHeight(只设置memCachedHeight时)模式的裁剪。

另外，对cached_net_image的cachedManager设置缓存策略也很重要。

##### 2.图片在服务端裁剪压缩。一般用户上传的图片如果不经过压缩，所占用的空间和流量是非常高的。服务端裁剪压缩要求按照图片显示空间所占的物理像素获取对应大小的图片，做法同本地的2x和3x图片，也要求按照2x和3x的要求裁剪压缩好存在服务端让客户端按需请求

综上,图片的缓存和裁剪压缩只花费很小的代价，却可以大幅降低网络流量费用、图片加载速度和本次磁盘空间占用量。

想快速知道哪些图片的分辨率超过了其实际的显示的分辨率，

##### 3. 让GPU将频繁使用的图片存到GPU的显存里面。在Image widget 外面包裹一个RepaintBoundary widget，则GPU有可能将图片缓存到显存,但不是一定的。

要知道图片最终是否被GPU缓存，只将MaterialApp的checkerboardRasterCacheImages参数设置为true,那么被GPU缓存的图片将显示成棋盘网格。如果同一个显示区域的图片缓存在不但更换，则该显示区域会呈现为闪烁的棋盘网格。
