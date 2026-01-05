
## 尺寸

- `width`
```css
.box {
  width: 300px;      /* 固定像素值 */
  width: 50%;        /* 父元素宽度的50% */
  width: 80vw;       /* 视口宽度的80% */
  width: auto;       /* 浏览器自动计算（默认值） */
}
```
- `height`
```css
.box {
	/* 同width */
  height: 200px;
  height: 100vh;     /* 视口高度的100% */
  height: inherit;   /* 继承父元素高度 */
}
```
- `line-height`：行高，设置行间距（文本行与行之间的垂直距离）
```css
p {
  line-height: 1.5;    /* 字体大小的1.5倍 */
  line-height: 24px;   /* 固定像素值 */
  line-height: 150%;   /* 字体大小的150% */
}
```
- `max-width`：设置元素的最大允许宽度
- `max-height`：设置元素的最大允许高度
- `min-width`：设置元素的最小允许宽度
- `min-height`：设置元素的最小允许高度

## BOX-SIZING

```css
box-sizing: content-box | border-box;
```

- `content-box`
	- 总宽度 = `width` + `padding` + `border` + `margin`
	- 总高度 = `height` + `padding` + `border` + `margin`
- `border-box`
	- 总宽度 = `width` + `margin`
	- 总高度 = `height` + `margin`

## 内/外边距

###  外边距`margin`

-  是元素边框**外**的透明区域，用于控制元素与**其他元素**之间的距离。

```css
/* 四个方向相同 */
margin: 10px;

/* 上下 | 左右 */
margin: 10px 20px;

/* 上 | 左右 | 下 */
margin: 10px 20px 15px;

/* 上 | 右 | 下 | 左 (顺时针) */
margin: 10px 15px 5px 20px;

/* 单独设置 */
margin-top: 10px;
margin-right: 15px;
margin-bottom: 5px;
margin-left: 20px;

margin: 0;     /* 无外边距 */
margin: auto;  /* 自动计算，常用于水平居中 */
```
- 实现元素水平居中：`margin: 0 auto;`

### 内边距`padding`

- 元素边框**内**的空白区域，用于控制元素内容与**自身边框**之间的距离。

```css
/* 写法与margin相同 */
padding: 10px;
padding: 10px 20px;
padding: 10px 20px 15px;
padding: 10px 15px 5px 20px;

/* 单独设置 */
padding-top: 10px;
padding-right: 15px;
padding-bottom: 5px;
padding-left: 20px;
```