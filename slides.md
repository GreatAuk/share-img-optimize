---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
colorSchema: 'dark'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS (experimental)
css: unocss
---

# 图片优化最佳实践

图片优化对于网站的性能和加载速度至关重要。优化图片可以减少网站的加载时间，提高用户体验，减少对用户的等待时间，降低网站的跳出率。此外，优化图片还可以减少带宽消耗，节省服务器资源，降低运维成本。
<div class="pt-12">
  <div @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    在现代 HTML 中， img 标签为我们提供了许多有用的属性来优化加载图像。让我们来看看它们 <carbon:arrow-right class="inline"/>
  </div>
</div>

<div class="abs-bl m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/GreatAuk/share-img-optimize" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>
<div
  v-click
  class="abs-br m-6 flex item-center text-3xl"
  v-motion
  :initial="{ x: -80 }"
  :enter="{ x: 0 }"
>
  吴展华
  <img src="https://avatars.githubusercontent.com/u/20253809?s=40&v=4" class="w-9 h-9 rounded-full ml-2" />
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# 一个案例
<div>
  你写了一个漂亮的页面，你需要加一个背景图，你会怎么做？
</div>
<v-click>
```css
.hero {
  /* 🚩 */
  background-image: url('/image.png');
}
```
</v-click>
<div v-click>
  <div>很常规的操作，但这样写有哪些缺点？</div>
  <div>知道有哪些优化方式？</div>
</div>

---

# background-image 的缺点
<div v-show="$slidev.nav.clicks < 2">
  1. 链式请求的问题
  <img class="my-4" src="https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202302261104351.jpeg" />

  > 在 CSS 中加载图像的情况下，假设您使用外部样式表（link rel=”styleshset” ，多数情况是这样的，而不是随处内联 style ），浏览器必须扫描您的 HTML，获取 CSS，然后找到一个 background-image 应用于一个元素，只有在所有这些之后才能获取该图像。这将需要更长的时间。

  <div class="mt-4" v-click>
    2. 无法使用 img 标签的额外优势。比如说 lazy loading、格式兼容、分辨率适配等等。
  </div>
</div>

---
layout: center
---

# 在现代 HTML 中， img 标签为我们提供了许多有用的属性来优化加载图像。让我们来看看它们。

---

# 图片类型

![](https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202304191829084.png)

---

## 图片格式总结
<div class="my-6 font-500">
  兼容性: WebP > AVIF > JPEG XL
</div>
<div v-click class="my-6 font-500">
  性能: JPEG XL > AVIF > WebP
</div>
<div v-click>JPEG XL、AVIF、Web 各自有各自的特点与优势，并且都未完全得到浏览器的支持。影响它们大规模使用的依旧是兼容问题。</div>

<div v-click class="mt-6">
  <div>相关：</div>
  <a href="https://www.bilibili.com/read/cv22543150?from=articleDetail" target="__blank">2023-03 bilibili-AVIF图片格式落地</a>
</div>

---

## 使用 picture 标签实现优雅降级
采用 HTML picture 标签提供若干 source 元素，并让浏览器根据其支持的图片格式自动选择对应的 URL。

<div class="grid grid-cols-2 gap-4 mt-6">

```html {all|3|4|7|all}
<picture>
  <!-- 性能表现更好的现代图片格式-->
  <source src="image.avif" type="image/avif">
  <source src="image.webp" type="image/webp">

   <!-- 最终的兜底方案-->
  <img src="image.jpg" type="image/jpeg">
</picture>
```
```html


<-- 1. 优先请求 AVIF 格式
<-- 2. 如果浏览器不支持，则降级到 WebP


<-- 3. 对于极少数不支持 WebP 的浏览器，则进一步降级到 JPG 格式。
.
```

</div>

---

# 原生的 lazy-loading

延迟加载图像，直到它和视口接近到一某个距离（由浏览器定义）

```html {all|2|all}
<img
  loading="lazy"
  ...
>
```
<v-click>

  > 注意！！！第一次加载时立即出现在浏览器视口中的图片，不要延迟加载。这将有助于确保最关键的图像尽快加载，而所有其他图像仅在需要时加载。

</v-click>

---

## loading="lazy" 的兼容性

<div class="mt-6">
  <div class="grid grid-cols-2 gap-4">
    <img src="https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202304192327685.png">
    <div v-click>
      <img src="https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202304192333221.png">
      <a class="mt-3 ml-4" href="https://github.com/element-plus/element-plus/blob/19e3164e6af2e5a781019e94608f6a662a1950c1/packages/components/image/src/image.vue#L148-L187" target="__blank">Element Plus 的 image 组件兼容方式</a>
    </div>
  </div>
</div>

<div class="mt-6">
  <v-clicks>
    <div class="mt-4 font-500">js 实现延迟加载</div>
    <div class="py-2">
      1. 通过 onscroll 事件与 getBoundingClientRect API 实现图片的延迟加载方案
    </div>
    <div>
      2. 通过 Intersection Observer 实现比监听 onscroll 性能更佳的图片延迟加载方案
    </div>
  </v-clicks>
</div>


---

# 异步图像解码
浏览器在进行图片渲染展示的过程中，是需要对图片文件进行解码的，这一个过程快慢与图片格式有关。
而如果我们不希望图片的渲染解码影响页面的其他内容的展示，可以使用 decoding="async" 选项

```html {all|2|all}
<img
  decoding="async"
  ...
>
```
<div v-click>
  这样，浏览器便会异步解码图像，加快显示其他内容。
</div>
<v-click>

它的可选取值如下：
- sync: 同步解码图像，保证与其他内容一起显示。
- async: 异步解码图像，加快显示其他内容。
- auto: 默认模式，表示不偏好解码模式。由浏览器决定哪种方式更适合用户

</v-click>

<div v-click class="mt-4">这是一个渐进增强方案, 不用考虑兼容性。</div>

---

# 下载优先级

<div>
  一个更高级的选项是 fetchpriority 。告诉浏览器图像是否具有超高优先级，例如您的 LCP 图像。
</div>

```html {all|2|all}
<img
  fetchpriority="high"
  ...
>
```

<v-click>

  或者，降低图像的下载优先级，如果你的图像位于首屏但重要性不高，如轮播图的非首张：

</v-click>

<v-click>

```html {all|2|3-4|all}
<div class="carousel">
  <img class="slide-1" fetchpriority="high">
  <img class="slide-2" fetchpriority="low">
  <img class="slide-3" fetchpriority="low">
</div>
```

</v-click>

---

# 避免布局偏移

<div v-show="$slidev.nav.clicks < 3">
  <div>如果没有在下载图像之前指定图像的大小比例，则在加载图像完成后可能会发生布局偏移。</div>
  <div class="mt-2" v-click>想象一下，你已经开始阅读一篇文章，可是页面上的文字突然位移（可能上面有的图片加载完成了），让你措手不及。</div>

  <img v-click class="w-2/3 mt-6" src="https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202304201633015.png" >
</div>



<div v-click v-show="$slidev.nav.clicks === 3">
  方法一：指定 width 和 height 属性。或只设置 width, 在 css 中设置 height 设置为 auto， 以便图像在屏幕尺寸变化时正确响应：

  ```html
  <img
    width="150"
    height="100"
    style="height: auto"
  >
  ```

</div>

<v-click>

  方法二：也可以只使用 CSS 中较新的 aspect-ratio 属性来始终自动获得正确的纵横比。使用此选项，你无需知道图像的确切宽度和高度，只需知道其纵横比即可：

```html {all|3|4|all}
<img
  style="
    width: 100%;
    aspect-ratio: 5 / 3;"
>
```

<div v-click class="my-2">

> aspect-ratio 不仅仅只能运用在 img。在 aspect-ratio 出现之前，我们只能通过一些其它的 Hack 方式，譬如设置 padding-top 值为百分比等方式模拟固定的宽高比。在 aspect-ratio 之后，我们终于有了设定容器固定宽高比的能力。

</div>

<div v-click class="my-2">

> aspect-ratio 也与 object-fit 和 object-position 搭配得很好，它们分别与背景图像的 background-size 和 background-position 非常相似。

</div>

<v-click>

```css
  .my-image {
    aspect-ratio: 5 / 3;
    width: 100%;
    /* Fill the available space, even if the image has a different intrinsic aspect ratio */
    object-fit: cover;
  }
```

</v-click>
</v-click>
---

# 为不同 DPR (设备像素比)，不同尺寸的屏幕选择最佳尺寸的图片

<div v-click>
  第一步，用 srcset 属性列出所有可用的图像。通过定义多个不同宽度的图像源，让浏览器在 HTML 解析期间选择最合适的图像源
</div>
<v-click>

```html {all|3-5|all}
<img
  src="photo.png"
  srcset="photo@1x.png 300w,    <!-- 宽度描述符就是图像原始的宽度，加上字符w -->
          photo@2x.png 600w,
          photo@3x.png 1200w"
>
```

</v-click>

<div v-click class="mt-2">
  第二步，sizes属性列出不同设备的图像显示宽度。
</div>

<v-click>

```html {all|2-3|all}
<img
  sizes="(min-width: 600px) 600px,   <!-- 屏幕当前的 CSS 像素宽度大于或者等于 600px，则图片的 CSS 宽度为 600px -->
        300px"
  src="photo.png"
  srcset="photo@1x.png 300w,
          photo@2x.png 600w,
          photo@3x.png 1200w"
>
```

</v-click>

<div v-click class="mt-2">
  第三步，浏览器根据当前设备的宽度，从sizes属性获得图像的显示宽度，然后从srcset属性找出最接近该宽度的图像，进行加载。
</div>

---

## 浏览器怎么确定当前场景要选取哪张图片呢？[Demo](https://codepen.io/Chokcoco/pen/WNeZvOX?editors=1100)

<div class="mt-6" v-show="$slidev.nav.clicks < 1">

  ##### **当前屏幕 dpr = 2 ，CSS 宽度为 375px**。

  当前屏幕 CSS 宽度为 375px，则图片 CSS 宽度为 300px。分别用上述 3 个宽度描述符的数值除以 300。

  1. 300 / 300 = 1
  2. 600 / 300 = 2
  3. 1200 / 300 = 4

  上面计算得到的 1、 2、 4 即是算出的有效的像素密度，换算成和 x 描述符等价的值 。这里 600w 算出的 2 即满足 dpr = 2 的情况，选择此张图。

</div>
<div class="mt-6" v-click v-show="$slidev.nav.clicks === 1">

  ##### **当前屏幕 dpr = 3 ，CSS 宽度为 414px**。

  当前屏幕 CSS 宽度为 414px，则图片 CSS 宽度仍为 300px。再计算一次：

  1. 300 / 300 = 1
  2. 600 / 300 = 2
  3. 1200 / 300 = 4

  因为 dpr = 3，2 已经不满足了，则此时会选择 1200w 这张图。
</div>

<div class="mt-6" v-click v-show="$slidev.nav.clicks === 2">

  ##### **当前屏幕 dpr = 1 ，CSS 宽度为 1920px**。

  当前屏幕 CSS 宽度为 1920px，则图片 CSS 宽度变为了 600px。再计算一次：

  1. 300 / 600 = .5
  2. 600 / 600 = 1
  3. 1200 / 600 = 2

  因为 dpr = 1，所以此时会选择 600w 对应的图片。

</div>

<div class="abs-br">

  ```html
  <img
    sizes="(min-width: 600px) 600px,
          300px"
    src="photo.png"
    srcset="photo@1x.png 300w,
            photo@2x.png 600w,
            photo@3x.png 1200w"
  >
  ```

</div>

---

# 图片的异常处理

当图片链接挂了，加载失败了，我们比较好的处理方式应该是怎么样呢？

<div v-show="$slidev.nav.clicks < 9">
<v-click>

处理的方式有很多种。在张鑫旭的这篇文章中 -- **[图片加载失败后CSS样式处理最佳实践](https://www.zhangxinxu.com/wordpress/2020/10/css-style-image-load-fail/)** 有一个不错的实践。

</v-click>

<v-click>

```html {1-3|4|all}
<img
  src="test.png"
  alt="Alt Info"
  onerror="this.classList.add('error');"
>
```

</v-click>

<v-click>

```css {all|5-8|9-11|all}
img.error {
  position: relative;
  display: inline-block;
}
img.error::before {
  content: "";
  background: url(error-default.png);
}
img.error::after {
  content: attr(alt);
}
```

</v-click>
</div>

<div v-click>

  我们利用伪元素 before ，加载默认错误兜底图，利用伪元素 after，展示图片的 alt 信息：

  ![图片](https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202304200006644.png)

</div>

---

# 如何使用 img 代替 background-image
<div> HTML </div>
```html
<div class="container">
  <picture class="bg-image">
    <source type="image/webp" ...>
    <img ...>
  </picture>
  <h1>我是背景上面的内容</h1>
</div>
```
<v-click>
<div class="mt-3"> CSS </div>

```css
  .container { position: relative; }
  .bg-image { position: absolute; inset: 0; }
  .bg-image img { width: 100%; height: 100%; object-fit: cover; }
```

</v-click>


<div v-click class="my-2">
  <div class="font-500 mb-2">使用这么多额外的 HTML 是否会影响性能？</div>
  <div>
    别忘记图像有多大（以字节为单位）。通过加载更优化的版本，向 HTML 添加几个字节可以为这些图像节省数千甚至数百万字节。
  </div>
</div>

<div v-click>
  <div class="font-500 mb-2">何时考虑 background-image?</div>
  如果你有一个小图片，你想用 background-repeat 平铺。如果使用 img, 没有简单的方法能实现类似效果。
</div>

---

# 总结

<v-clicks>

* picture 实现图片格式的优雅降级。
* loading="lazy" 实现图片的延迟加载。
* srcset & sizes 实现图片的响应式。
* decoding="async" 实现图片的异步解码。
* 设置图片的 aspect-ratio，避免布局偏移。
* fetchpriority 属性控制图片的下载优先级。
* 图片加载失败时，显示错误兜底图片。
* 设置图片的 alt、aria-label、aria-labelledby 属性，提升图片的可访问性。
* 尽可能的使用 img 而不是 CSS background-image。

</v-clicks>

---

# 更简单的方法
<v-clicks>

* [unpic-img](https://github.com/ascorbic/unpic-img)
* [Nuxt Image](https://image.nuxtjs.org/)
* [Next Image](https://nextjs.org/docs/api-reference/next/image)
* [Qwik Image](https://github.com/BuilderIO/qwik/pull/2860) (在路上了)

</v-clicks>
