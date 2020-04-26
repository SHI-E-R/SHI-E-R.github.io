## 介绍

`canvas`是`H5`中新加的一个标签

html

```
<canvas width='1200' height='800'> </canvas>
```

标签主要的属性如图,仅需`width`和`height`两个

就俩? 看着很简单不是? 当然不是 ╮(╯▽╰)╭ , 因为`canvas`是一个需要和`js`结合使用的标签

html

```
<body>
    <canvas width='1200' height='800' id='canvas'> </canvas>
</body>

<script>
    const canvas = document.getElementById('canvas')
    const ctx = canvas.getContext('2d') // 2d ? 有3d咩 ? 有 => webgl :)
    ...
</script>
```

`canvas`拿的是画板, `ctx`拿的是画笔, 画图主要的是通过画笔不是, 所以我们使用的也基本上是`ctx`身上的属性和方法, `ctx`身上有许多画图的方法:

- 画线的 `lineTo(x, y)`
- 画矩形的 `rect(x, y, width, height)`
- 画弧 / 圆的 `arc(x, y, r, startAng, endAng, true/false)`
- 画文本的 `fillText('Hello World!')`
- 画图片的 `drawImage(imgData, x, y, width, height)`
- …

以上只是简写, 有许多细节并未说明, 想细学的可以去

[w3c](https://www.w3school.com.cn/tags/html_ref_canvas.asp) 或 [mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Basic_usage)

本文只介绍`canvas`对图片的相关操作,也就是围绕`drawImage`方法展开

> Tips: 以下操作都在node服务器下操作, 文本域下会存在跨域问题, 无法实现一些操作

## 玩法一: 图片的组合

在工作中难免会遇到需要前端画图的场景,根据用户的选择,动态的生成海报之类的,这时候就需要使用我们的`canvas`了,先看代码

html

```
<body>
    <canvas width='384' height='683' id='canvas'></canvas> // ? => 和图片大小相关
</body>

<script>
    const canvas = document.getElementById('canvas')
    const ctx = canvas.getContext('2d')
    咱继续 (*^▽^*)
    const img1 = new Image()
    img1.src = './qiu.png'
    img1.onload = () => {
      ctx.drawImage(img1, 0, 0, canvas.width, canvas.height)
    }
</script>
```

`ctx.drawImage(imgdata, x, y, width, height)`方法的参数有5个,分别是:

- `x` 绘制图像的左上点x轴坐标
- `y` 绘制图像的左上点y轴坐标
- `width` 绘制图像的宽度
- `height` 绘制图像的高度
- `imgdata` 这个在MDN的叫法为`CanvasImageSource`说人话是`canvas图像源`, 还听不懂?没关系我也不懂,总之常用的就是本文中的`HTMLImageElement`和`HTMLVideoElement`

```
HTMLImageElement`和`HTMLVideoElement`又是啥? 哈哈,其实就是`img标签`和`video标签
```

javascript

```
const img1 = new Image() 
等于
const img1 = document.createElement('img')
也等于
<img id='img' src=''></img>
const img1 = document.getElementById('img')

<video>标签也可以哦
```

此时的界面

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786455459249.png)

空荡荡的篮球显得很孤独 (ノへ￣、) , 所以,我们得为他加个伴

javascript

```
const canvas = document.getElementById('canvas')
const ctx = canvas.getContext('2d')
const img1 = new Image()
const img2 = new Image()
img1.src = './qiu.png'
img1.onload = () => {
	ctx.drawImage(img1, 0, 0, canvas.width, canvas.height)
    img2.src = './cai.png'
    img2.onload = () => {
       ctx.drawImage(img2, 40, 180, 315, 453)
    }
}
```

看图

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786462963718.png)

完美! (≧∀≦)ゞ

> `tips1`: 注意要在`img1`加载完成后的回调函数中在给`img2`赋值`src`, 因为后绘制的会抹除先绘制的, 会出现f覆盖关系错误的问题

> `tips2:` 这拙略的抠图, 辣眼睛 : (

以上就是`canvas`对图像进行组合的方法, `so easy`是不是!

## 玩法二 下载

图片组合好了, 该怎么保存(下载)呢?

首先我们得知道下载可分为两种:

1. 本地下载
2. 服务端下载

本地下载是指纯前端操作, 不经过后端接口直接保存, 而服务端下载则是通过接口返回的图片数据下载

本次只讲本地下载 , 服务端下载我会在下篇`node文章中讲明`

那本地下载咋下呢? 看下图你就知道了:

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786482003291.png)

没错, 就是右键另存为 (>▽<)

**啥? 你不听? 你不会右键?? 你要能看的见的下载???**

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1578658597377&di=a52b8230ee175e68838d27940ff188cd&imgtype=0&src=http%3A%2F%2Fd.ifengimg.com%2Fw600%2Fe0.ifengimg.com%2F06%2F2018%2F1219%2FB3D9577023DD2CA4878398E582155F56045D0A8D_size17_w640_h441.jpeg)

好吧, 谁让我是个`好人`呢

先来个按钮

html

```
<canvas id='ctx' width="384" height="683"></canvas>
<p><button onclick='download()'>下载</button></p>
```

如图

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786487653170.png)

javascript

```
base64_img = canvas.toDataURL()
const download = () => {
    const a = document.createElement('a')
    a.href = base64_img
    a.download = 'download.jpg'
    a.click()
}
```

`canvas.toDataURL()`方法是将画布(canvas)的信息转为`base64`格式

`download`方法则是模拟一个`a标签`点击, 此方法确实可以下载, 但有个小弊端就是下载后的图片会偏大(存储的大小), 这是由于转为`base64`格式所造成的

以上就是`canvas`图像的下载, 有些瑕疵, 但目前我也没找到别的好方法, 以后我会了会补全这篇文章的!

## canvas像素操作 - 图片滤镜

换张图

html

```
<canvas id='ctx' width="1200" height="800"></canvas>
<p><button onclick='blackWhite()'>黑白滤镜</button></p>
<script>
  const canvas = document.getElementById('ctx')
  const ctx = canvas.getContext('2d')
  const img = new Image()
  img.src = './flowers.webp'
  img.onload = () => {
    ctx.drawImage(img, 0, 0, canvas.width, canvas.height)
  }
</script>
```

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786505139106.png)

**本来想多举几个例子, 但写到这里有点晕晕的, 这里就举一个黑白滤镜的例子把**

继续

javascript

```
const blackWhite = () => {
  let myData = ctx.getImageData(0, 0, canvas.width, canvas.height)
  let arr = []
  for(let i = 0; i < myData.data.length; i++) {
    if ((i+1)%4 == 0) {
      let total = (myData.data[i - 1] + myData.data[i - 2] + myData.data[i - 3]) / 3
      myData.data[i - 1] = myData.data[i - 2] = myData.data[i - 3] = total
    }
  }
  ctx.putImageData(myData, 0, 0)
}
ctx.getImageData(0, 0, canvas.width, canvas.height)
```

是拿到该画布上的所有像素点, 返回的格式是一个巨大的数组:

javascript

```
[255, 255, 0, 255, ...]
```

四位为一个像素点, 分别对应R = 255, G = 255, B == 0, A == 255, 也就是说改点是一个透明度为1的纯黄色像素点,知道了这个后, 想弄成黑白滤镜就有了方向

我们还得知道一个小知识, 那就是, 当R,G,B值相同时会显示为灰色, 且数值越大越亮, 如255, 255, 255 为白色

所以我们可以将每项的元素点的R,G,B加起来除以三赋值给他们, 就实现了黑白滤镜, 如下

javascript

```
let total = (myData.data[i - 1] + myData.data[i - 2] + myData.data[i - 3]) / 3
myData.data[i - 1] = myData.data[i - 2] = myData.data[i - 3] = total
```

`ctx.putImageData(myData, 0, 0)`是将处理后的像素点又绘制到画布上

结果

![img](https://img.mrpzx.cn/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15786512848682.png)

## 总结

以上就是`canvas`对图片的一些操作能力, 另外水平有限, 写的不好请谅解

最后

写的真的有点晕, 最后有点潦草了

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1578661700865&di=68b36c83521bfc6f757facf81b172ac4&imgtype=0&src=http%3A%2F%2Fhbimg.b0.upaiyun.com%2Fc5d403de7d606fcfe802122809529c1ed5fda82816c6c-pF4icn_fw658)

**文章作者:** [Mrpzx](mailto:undefined)

**文章链接:** [http://blog.mrpzx.cn/2020/01/07/%E5%88%A9%E7%94%A8canvas%E7%8E%A9%E8%BD%AC%E5%9B%BE%E7%89%87/](http://blog.mrpzx.cn/2020/01/07/利用canvas玩转图片/)

**版权声明:** 本博客所有文章除特别声明外，均采用 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) 许可协议。转载请注明来自 [Mrpzx](http://blog.mrpzx.cn/)！

> - **本文链接：** [http://yoursite.com/2020/01/13/%E5%88%A9%E7%94%A8canvas%E7%8E%A9%E8%BD%AC%E5%9B%BE%E7%89%87/](http://yoursite.com/2020/01/13/利用canvas玩转图片/)
> - **版权声明：** 本博客所有文章除特别声明外，均采用 [CC BY 4.0 CN协议](http://creativecommons.org/licenses/by/4.0/deed.zh) 许可协议。转载请注明出处！