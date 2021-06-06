# cube
A spinning cube.

Check out the [demo here](http://yanhaixiang.com/cube/).

## 如何实现

最近在 [http://algoexpert.io/](http://algoexpert.io/) 上看到一个非常好玩的东西。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4bb796af8c8142eb980c45b4423442c2~tplv-k3u1fbpfcp-zoom-1.image)

刚开始的时候，我还要以为要用到什么 JS 库来实现的，打开控制台研究了一下，原来用 CSS 就可以实现了，好吧，算我孤陋寡闻了，哈哈。于是，写篇博客记录一下吧。

## 6 个面

首先，在 HTML 弄 6 个面。

```html
<div class="cube-container">
  <div class="cube-faces">
    <div class="cube-face cube-face-front">
      <img src="https://yanhaixiang.com/cube/images/js.png" alt="js">
    </div>
    <div class="cube-face cube-face-back">
      <img src="https://yanhaixiang.com/cube/images/python.png" alt="python">
    </div>
    <div class="cube-face cube-face-top">
      &nbsp;
    </div>
    <div class="cube-face cube-face-bottom">
      &nbsp;
    </div>
    <div class="cube-face cube-face-left">
      <img src="https://yanhaixiang.com/cube/images/golang.png" alt="golang">
    </div>
    <div class="cube-face cube-face-right">
      <img src="https://yanhaixiang.com/cube/images/cpp.png" alt="cpp">
    </div>
  </div>
</div>
```

非常简单，container 作为正方体容器，cube-faces 作为 6 个面的容器，剩下的 div 就是 6 个面，里面放一个 img。

效果就是一列排下来：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4b40bd7cd2884a698069fa2bf2587260~tplv-k3u1fbpfcp-zoom-1.image)


## 集合 6 个面

第二步，就是将这 6 个面堆叠在一起。很简单，只要 .cube-faces 设置 position: relative，然后 .cube-face 设置 position: absolute 就可以使得 6 个面都脱离文档流叠在一起了。

```css
.cube-faces {
  position: relative;
  width: 300px;
}

.cube-face {
  position: absolute;
  height: 300px;
  width: 300px;
  outline: 1px solid #02203c;
  box-shadow: inset 0 0 100px #02203c;
  background-color: #3e526a;
  opacity: 0.75;
}

.cube-face img {
  width: 100%;
  height: 100%;
}
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd7e2e3b10c14e47a93100598a4b9ce5~tplv-k3u1fbpfcp-zoom-1.image)

## 旋转平面

现在还是一个平面结构，下面将这 6 个面通过 transform: rotate 来做旋转使得其像个正方体。

```css
.cube-face.cube-face-front {
  transform: translateZ(150px);
}

.cube-face.cube-face-back {
  transform: translateZ(-150px) rotateY(180deg);
}

.cube-face.cube-face-top {
  transform: rotateX(-90deg) translateY(-150px);
  transform-origin: top center;
}

.cube-face.cube-face-bottom {
  transform: rotateX(90deg) translateY(150px);
  transform-origin: bottom center;
}

.cube-face.cube-face-left {
  transform: rotateY(270deg) translateX(-150px);
  transform-origin: left center;
}

.cube-face.cube-face-right {
  transform: rotateY(-270deg) translateX(150px);
  transform-origin: top right;
}
```

很遗憾，出来的效果还是一个“平面”。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b0083bd45ac4192943d186bc69164fa~tplv-k3u1fbpfcp-zoom-1.image)

首先，这 6 个面其实已经转成了正方体了，但是因为我们目光只朝着一个面看，所以看起来还是二维的。但是也不对呀，如果已经变成立方体，那我们看到的应该是 JS 这一面而不是 Python 这面呀，Python 不是旋转到后面了么？

虽然 Python 是作为后背面，但是在 HTML 里，python 的 div 在 JS 的 div 之后，所以优先显示 Python。

为了去掉 HTML 的顺序影响，可以在 .cube-faces 添加：

```css
.cube-faces {
  position: relative;
  width: 300px;
  transform-style: preserve-3d; /* 3D */
}
```

现在立方体的 JS 面就朝着我们来了：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69ffa64a5f5048d3b3a7b883550447f4~tplv-k3u1fbpfcp-zoom-1.image)

## 立起来

下面隆重介绍 CSS 的 perspective 属性，可以把它理解为我们的目光放在哪里。刚刚说到，我们看到二维面是因为我们目光死死盯着一个面，所以我们只需要将目光往上抬一下，从上往下看整个立方体，立方体就“立体”起来了。

```css
/*将立方体放中间*/
body {
  display: flex;
  align-items: center;
  justify-content: center;
}

.cube-container {
  margin-top: 200px;
  perspective: 800px; /* 目光延长线 */
  perspective-origin: 50% 100px; /* 目光位置水平居中，往上抬 100px */
}
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb4a8c88d5364b158500572aa9cd9d20~tplv-k3u1fbpfcp-zoom-1.image)

开始有那味了。

## 转起来

下一步，为了让立方体有动感一点，定义一个 @keyframes 动画：

```css
@keyframes spin { /* Y轴旋转 */
  0% {
    transform: rotateY(0);
  }
  100% {
    transform: rotateY(360deg);
  }
}

.cube-faces {
  position: relative;
  width: 300px;
  transform-style: preserve-3d;
  animation: spin 10s infinite linear; /*动画*/
}
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82413225f75749d28397bd44be2d9cd3~tplv-k3u1fbpfcp-zoom-1.image)

## 加点阴影

现在的立方体已经很立体了，但是总感觉很假的样子，这是因为缺少阴影的原因。我们要以在底部那个 div 加点小阴影：

```css
.cube-face.cube-face-bottom {
  transform: rotateX(90deg) translateY(150px);
  transform-origin: bottom center;
  box-shadow: 0 0 100px #000; /*阴影*/
}
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a092fa519d924c08ab6a35edfabcea74~tplv-k3u1fbpfcp-zoom-1.image)

大功告成！
