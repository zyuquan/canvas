@[TOC]目录

+ [基本用法](#基本用法)  
    + [canvas用法](#canvas用法)  
    + [渲染上下文](#渲染上下文)  
    + [检查支持项](#检查支持项)  
+ [绘制图形](#绘制图形)  
    + [绘制矩形方法](#绘制矩形方法)  
    + [绘制路径和线](#绘制路径和线)  
    + [圆弧](#圆弧)  
    + [二次贝塞尔曲线及三次贝塞尔曲线](#二次贝塞尔曲线及三次贝塞尔曲线)  
    + [Path2D对象](#Path2D对象)  
+ [添加样式和颜色](#添加样式和颜色)  
    + [色彩Color](#色彩Color)  
    + [透明度](#透明度)  
    + [线型LineStyle](#线型LineStyle)  
    + [渐变](#渐变)  
    + [图案样式](#图案样式)  
    + [阴影](#阴影)  
    + [Canvas填充规则](#Canvas填充规则)  
+ [绘制文本](#绘制文本)  
    + [样式](#样式)  
+ [使用图像](#使用图像)  
+ [变形](#变形)  
+ [合成与裁剪](#合成与裁剪)  
    + [裁切路径](#裁切路径)  
+ [动画](#动画)  
+ [像素操作](#像素操作)  
    + [ImageData对象](#ImageData对象)  
    + [创建一个ImageData对象](#创建一个ImageData对象)  
    + [得到场景像素数据](#得到场景像素数据)  
    + [在场景中写入像素数据](#在场景中写入像素数据)  
    + [缩放和反锯齿](#缩放和反锯齿)  
    + [保存图片](#保存图片)  
## 基本用法
#### canvas用法
```
    <canvas id="canvas" width="150" height="150"></canvas>
```
<canvas> 标签只有两个属性—— width和height。这些都是可选的，并且同样利用 DOM properties 来设置。当没有设置宽度和高度的时候，canvas会初始化宽度为300像素和高度为150像素。该元素可以使用CSS来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果CSS的尺寸与初始画布的比例不一致，它会出现扭曲。  
>__注意__: 如果你绘制出来的图像是扭曲的, 尝试用width和height属性为<canvas>明确规定宽高，而不是使用CSS。  
#### 渲染上下文
<canvas> 元素创造了一个固定大小的画布，它公开了一个或多个渲染上下文，其可以用来绘制和处理要展示的内容。我们将会将注意力放在2D渲染上下文中。其他种类的上下文也许提供了不同种类的渲染方式；比如， WebGL 使用了基于OpenGL ES的3D上下文 ("experimental-webgl") 。  
canvas起初是空白的。为了展示，首先脚本需要找到渲染上下文，然后在它的上面绘制。<canvas> 元素有一个叫做 getContext() 的方法，这个方法是用来获得渲染上下文和它的绘画功能。getContext()只有一个参数，上下文的格式。对于2D图像而言，如本教程，你可以使用 CanvasRenderingContext2D。  
```
    var canvas = document.getElementById("canvas");
    var ctx = canvas.getContext("2d");
```
代码的第一行通过使用 document.getElementById() 方法来为 <canvas> 元素得到DOM对象。一旦有了元素对象，你可以通过使用它的getContext() 方法来访问绘画上下文。  
#### 检查支持项
替换内容是用于在不支持 <canvas> 标签的浏览器中展示的。通过简单的测试getContext()方法的存在，脚本可以检查编程支持性。上面的代码片段现在变成了这个样子：  
```
    var canvas = document.getElementById("canvas");
    if (canvas.getContext) {
        var ctx = canvas.getContext("2d");
    } else {
        \\
    }
```

## 绘制图形
如何在canvas上绘制三角形、直线、圆弧和曲线。须知左上角为原点坐标，所有元素的位置都相对于原点定位。  
#### 绘制矩形方法
```
    rect(x, y, width, height) // 绘制一个矩形
    fillRect(x, y, width, height) // 绘制一个填充矩形
    strokeRect(x, y, width, height) // 绘制一个矩形的边框
    clearRect(x, y, width, height) // 清楚指定矩形区域，让清除部分完全透明
```
矩形例子
```
    function draw() {
        var canvas = document.getElementById("canvas");
        if (canvas.getContext) {
            var ctx = canvas.getContext("2d");
            // 绘制一个起点在(25, 25)位置宽100，高100的矩形
            ctx.fillRect(25, 25, 100, 100);
            // 清除一个起点在(45, 45)位置宽60，高60的矩形
            ctx.clearRect(45, 45, 60, 60);
            // 绘制一个起点在(50, 50)位置宽50，高50的矩形边框
            ctx.strokeRect(50, 50, 50, 50);
        }
    }
```
#### 绘制路径和线
图形的基本元素是路径。路径是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点的集合。一个路径，甚至一个子路径，都是闭合的。使用路径绘制图形需要额外的步骤。  
    1.首先，你需要创建路径起始点。  
    2.然后你使用画图命令去画出路径。  
    3.之后闭合路径。  
    4.生成路径后，你可以通过通过描边或填充来渲染图形。  
一下是用到的函数：  
```
    beginPath() // 新建一条路径的起点
    closePath() // 闭合路径，绘制命令重新指向上下文

    moveTo(x, y) // 将画笔移动到指定的(x, y)位置
    lineTo(x, y) // 绘制一条直线从当前位置到(x, y)位置

    stroke() // 绘制轮廓
    fill() // 绘制填充
```
__示例__
```
    const canvas = document.getElementById("canvas");
    const cxt = canvas.getContext("2d");
    cxt.fillRect(200, 100, 400, 200);
    cxt.clearRect(250, 150, 300, 100);
    cxt.strokeRect(300, 175, 200, 50);

    cxt.fillStyle = "#000000";
    cxt.rect(0, 0, 200, 100);
    cxt.fill();

    cxt.strokeStyle = "#000000";
    cxt.rect(600, 300, 200, 100);
    cxt.stroke();
```
<img src="./images/1.jpg" >

> __注意__：当前路径为空，即调用beginPath()之后，或者canvas刚建的时候，第一条路径构造命令通常被视为是moveTo（），无论实际上是什么。出于这个原因，你几乎总是要在设置路径之后专门指定你的起始位置。  

> __注意__：当你调用fill()函数时，所有没有闭合的形状都会自动闭合，所以你不需要调用closePath()函数。但是调用stroke()时不会自动闭合。  
#### 圆弧
绘制圆弧或者圆，我们使用arc()方法。当然可以使用arcTo()，不过这个的实现并不是那么的可靠，所以我们这里不作介绍。  
```
    // (x, y)为圆心位置,radius为半径，startAngle和endAngle为开始和结束弧度，
    // anticlockwise为绘制圆弧的方向
    arc(x, y, radius, startAngle, endAngle, anticlockwise)

    // (x1, y1)第一个控制点，(x2, y2)第二个控制点，radius半径
    arcTo(x1, y1, x2, y2, radius)
```
> 注意：arc()函数中表示角的单位是弧度，不是角度。角度与弧度的js表达式: 弧度=(Math.PI/180)*角度。  
#### 二次贝塞尔曲线及三次贝塞尔曲线
```
    // 绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
    quadraticCurveTo(cp1x, cp1y, x, y)

    // 绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。
    bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)
```
#### Path2D对象
__Path2D__  
    Path2D()会返回一个新初始化的Path2D对象（可能将某一个路径作为变量——创建一个它的副本，或者将一个包含SVG path数据的字符串作为变量）。  
```
    new Path2D();     // 空的Path对象
    new Path2D(path); // 克隆Path对象
    new Path2D(d);    // 从SVG建立Path对象
```
所有的路径方法比如moveTo, rect, arc或quadraticCurveTo等，如我们前面见过的，都可以在Path2D中使用。  
Path2D API 添加了 addPath作为将path结合起来的方法。当你想要从几个元素中来创建对象时，这将会很实用。比如：  
__Path2D.addPath(path [, transform])​__  
添加了一条路径到当前路径（可能添加了一个变换矩阵）。  
__使用 SVG paths__  
```
    var p = new Path2D("M10 10 h 80 v 80 h -80 Z");
```

## 添加样式和颜色
#### 色彩Color
如果想要给图形上色，有两个重要的属性可以做到：  
__fillStyle = collor__ 设置图形的填充颜色  
__strokeStyle = collor__ 设置图形轮廓的颜色  
> __注意:__ 一旦您设置了 strokeStyle 或者 fillStyle 的值，那么这个新值就会成为新绘制的图形的默认值。如果你要给每个图形上不同的颜色，你需要重新设置 fillStyle 或 strokeStyle 的值。  
#### 透明度
__globalAlpha = value__  
这个属性影响到 canvas 里所有图形的透明度，有效的值范围是 0.0 （完全透明）到 1.0（完全不透明），默认是 1.0。  
#### 线型LineStyle
__lineWidth = value__ 设置线条宽度  
__lineCap = type__ 设置线末端样式  
```
ctx.lineCap = "butt"; // 线段末端以方形结束
ctx.lineCap = "round"; // 线段末端以圆形结束
ctx.lineCap = "square"; // 线段末端以方形结束，但是增加了一个宽度和线段相同，高度是线段厚度一半的矩形区域
```
__lineJoin = type__ 设置线条与线条结合处的样式  
```
// 在相连部分的末端填充一个额外的以三角形为底的区域， 每个部分都有各自独立的矩形拐角
ctx.lineJoin = "bevel";
// 通过填充一个额外的，圆心在相连部分末端的扇形，绘制拐角的形状。 圆角的半径是线段的宽度
ctx.lineJoin = "round";
// 通过延伸相连部分的外边缘，使其相交于一点，形成一个额外的菱形区域
ctx.lineJoin = "miter";
```
__miterLimit = value__ 限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度  
__getLineDash()__ 返回一个包含当前虚线样式，长度为非负偶数的数组  
__setLineDash(segments)__ 设置当前虚线样式
__lineDashOffset = value__ 设置虚线样式的起始偏移量
#### 渐变
__createLinearGradient(x1, y1, x2, y2)__  
createLinearGradient 方法接受 4 个参数，表示渐变的起点 (x1,y1) 与终点 (x2,y2)  
__createRadialGradient(x1, y1, r1, x2, y2, r2)__  
createRadialGradient 方法接受 6 个参数，前三个定义一个以 (x1,y1) 为原点，半径为 r1 的圆，后三个参数则定义另一个以 (x2,y2) 为原点，半径为 r2 的圆。  
```
var lineargradient = ctx.createLinearGradient(0,0,150,150);
var radialgradient = ctx.createRadialGradient(75,75,0,75,75,100);
```
创建出 canvasGradient 对象后，我们就可以用 addColorStop 方法给它上色了。  
__gradient.addColorStop(position, color)__  
addColorStop 方法接受 2 个参数，position 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。例如，0.5 表示颜色会出现在正中间。color 参数必须是一个有效的 CSS 颜色值（如 #FFF， rgba(0,0,0,1)，等等）  
#### 图案样式
__createPattern(image, type)__  
该方法接受两个参数。Image 可以是一个 Image 对象的引用，或者另一个 canvas 对象。Type 必须是下面的字符串值之一：repeat，repeat-x，repeat-y 和 no-repeat  
```
var img = new Image();
img.src = 'someimage.png';
var ptrn = ctx.createPattern(img,'repeat');
```
#### 阴影
__shadowOffsetX = float__  
shadowOffsetX 和 shadowOffsetY 用来设定阴影在 X 和 Y 轴的延伸距离，它们是不受变换矩阵所影响的。负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0。  
__shadowOffsetY = float__  
shadowOffsetX 和 shadowOffsetY 用来设定阴影在 X 和 Y 轴的延伸距离，它们是不受变换矩阵所影响的。负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0。  
__shadowBlur = float__  
shadowBlur 用于设定阴影的模糊程度，其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0。  
__shadowColor = color__  
shadowColor 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色。  
#### Canvas填充规则
__ctx.fill(value)__  
两个可能的值：  
+ nonzero：默认值
+ evenodd：奇偶环绕规则

## 绘制文本
canvas提供了两个绘制文本的方法分别是  
```
// 指定文本内容text, 位置(x, y), 最大宽度maxWidth(可选)
fillText(text, x, y, maxWidth)
//
strokeText(text, x, y, maxWidth)
```
#### 样式
__font = value__  
__textAligin = value__ 文本对齐选项,可选值start, end, left, right or center. 默认值是 start  
__textBaseline = value__ 基线对齐选项,可选值top, hanging, middle, alphabetic, ideographic, bottom。默认值是 alphabetic  
__direction = value__ 文本方向,可选值ltr, rtl, inherit。默认值是 inherit  

## 使用图像
引入图像到canvas里需要以下两步基本操作：  
+ 获得一个指向HTMLImageElement的对象或者另一个canvas元素的引用作为源，也可以通过提供一个URL的方式来使用图片
+ 使用drawImage()函数将图片绘制到画布上
```
// image指定的图像， (x, y)位置
ctx.drawImage(image, x, y)
// image指定的图像， (x, y)位置，(width, height)图片的大小
ctx.drawImage(image, x, y, width, height)
// image指定的图像， (sx, sy)指定切片的位置，(sWidth, sHeight)切片的大小，
// (x, y)指定剪切的图片放在画布的位置，(width, height)设置剪切下来的图片在画布的大小
ctx.drawImage(image, sx, sy, sWidth, sHeight, x, y, width, height)
```

## 变形
在了解变形之前，我先介绍两个在你开始绘制复杂图形时必不可少的方法  
__save()__ 保存当前画布所有状态  
__restore()__ save 和 restore 方法是用来保存和恢复 canvas 状态的，都没有参数。Canvas 的状态就是当前画面应用的所有样式和变形的一个快照  
Canvas状态存储在栈中，每当save()方法被调用后，当前的状态就被推送到栈中保存。一个绘画状态包括：  
+ 当前应用的变形
+ 以及下面这些属性：strokeStyle, fillStyle, globalAlpha, lineWidth, lineCap, lineJoin, miterLimit, lineDashOffset, shadowOffsetX, shadowOffsetY, shadowBlur, shadowColor, globalCompositeOperation, font, textAlign, textBaseline, direction, imageSmoothingEnabled
+ 当前的裁切路径（clipping path）
你可以调用任意多次 save方法。每一次调用 restore 方法，上一个保存的状态就从栈中弹出，所有设定都恢复  
变形的方法有以下几种：  
+ translate(x, y) 方法接受两个参数。x 是左右偏移量，y 是上下偏移量
+ scale(x, y) 方法可以缩放画布的水平和垂直的单位。两个参数都是实数，可以为负数，x 为水平缩放因子，y 为垂直缩放因子，如果比1小，会比缩放图形， 如果比1大会放大图形。默认值为1， 为实际大小
+ rotate(angle) 这个方法只接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值
+ transform(a, b, c, d, e, f)
    + a (m11) 水平方向的缩放
    + b(m12) 水平方向的倾斜偏移
    + c(m21) 竖直方向的倾斜偏移
    + d(m22) 竖直方向的缩放
    + e(dx) 水平方向的移动
    + f(dy) 竖直方向的移动
+ setTransform(a, b, c, d, e, f) 这个方法会将当前的变形矩阵重置为单位矩阵，然后用相同的参数调用 transform 方法。如果任意一个参数是无限大，那么变形矩阵也必须被标记为无限大，否则会抛出异常。从根本上来说，该方法是取消了当前变形,然后设置为指定的变形,一步完成
+ resetTransform() 重置当前变形为单位矩阵，它和调用以下语句是一样的：ctx.setTransform(1, 0, 0, 1, 0, 0)
> __注意：__ 变形改变的是坐标系

## 合成与裁剪
我们不仅可以在已有图形后面再画新图形，还可以用来遮盖指定区域，清除画布中的某些部分（清除区域不仅限于矩形，像clearRect()方法做的那样）以及更多其他操作  
__globalCompositeOperation = type__ 这个属性设定了在画新图形时采用的遮盖策略，其值是一个标识12种遮盖方式的字符串。  
+ source-over 默认设置，在现有画布上下文之上绘制新图形
+ source-in 新图形和目标画布重叠部分绘制，其他部分透明
+ source-out 保留新图形不在目标画布绘制部分
+ source-atop 保留目标画布部分，不在目标画布部分透明
+ destination-over 目标画布覆盖在新图形上面
+ destination-in 目标画布覆盖在新图形上面，保留相交部分
+ destination-out 目标画布覆盖在新图形上面，保留目标画布未相交部分
+ destination-atop 目标画布覆盖在新图形上面，保留新图形
+ copy 显示新图形
+ xor 重叠部分透明
+ multiply 将顶层像素与底层相应像素相乘，结果是一幅更黑暗的图片
+ screen 像素被倒转，相乘，再倒转，结果是一幅更明亮的图片
+ overlay multiply和screen的结合，原本暗的地方更暗，原本亮的地方更亮
+ darken 保留两个图层中最暗的像素
+ lighten 保留两个图层中最亮的像素
+ color-dodge 将底层除以顶层的反置
+ color-burn 将反置的底层除以顶层，然后将结果反过来
+ hard-light 屏幕相乘（A combination of multiply and screen）类似于叠加，但上下图层互换了
+ soft-light 用顶层减去底层或者相反来得到一个正值
+ difference 一个柔和版本的强光（hard-light）。纯黑或纯白不会导致纯黑或纯白
+ exclusion 和difference相似，但对比度较低
+ hue 保留了底层的亮度（luma）和色度（chroma），同时采用了顶层的色调（hue）
+ saturation 保留底层的亮度（luma）和色调（hue），同时采用顶层的色度（chroma）
+ color 保留了底层的亮度（luma），同时采用了顶层的色调(hue)和色度(chroma)
+ luminosity 保持底层的色调（hue）和色度（chroma），同时采用顶层的亮度（luma）
```
const ctx = document.getElementById("canvas").getContext("2d");
//
ctx.fillStyle = "#0f0f0f";
ctx.arc(60, 60, 40, 0, 2*Math.PI);
ctx.fill();
//
ctx.fillStyle = "#f0f0f0";
ctx.beginPath();
ctx.globalCompositeOperation = "source-in";
ctx.arc(80, 80, 40, 0, 2*Math.PI);
ctx.fill();
```
#### 裁切路径
裁切路径和普通的 canvas 图形差不多，不同的是它的作用是遮罩，用来隐藏不需要的部分  
在 绘制图形 一章中，我只介绍了 stroke 和 fill 方法，这里介绍第三个方法clip  
__clip()__ 将当前正在构建的路径转换为当前的裁剪路径
```
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
ctx.arc(75, 75, 50, 0, 2*Math.PI);
ctx.clip();

// 创建渐变
var radgrad = ctx.createRadialGradient(45,45,10,52,50,30);
radgrad.addColorStop(0, '#A7D30C');
radgrad.addColorStop(0.9, '#019F62');
radgrad.addColorStop(1, 'rgba(1,159,98,0)');

var radgrad2 = ctx.createRadialGradient(105,105,20,112,120,50);
radgrad2.addColorStop(0, '#FF5F98');
radgrad2.addColorStop(0.75, '#FF0188');
radgrad2.addColorStop(1, 'rgba(255,1,136,0)');

var radgrad3 = ctx.createRadialGradient(95,15,15,102,20,40);
radgrad3.addColorStop(0, '#00C9FF');
radgrad3.addColorStop(0.8, '#00B5E2');
radgrad3.addColorStop(1, 'rgba(0,201,255,0)');

var radgrad4 = ctx.createRadialGradient(0,150,50,0,140,90);
radgrad4.addColorStop(0, '#F4F201');
radgrad4.addColorStop(0.8, '#E4C700');
radgrad4.addColorStop(1, 'rgba(228,199,0,0)');

// 画图形
ctx.fillStyle = radgrad4;
ctx.fillRect(0,0,150,150);
ctx.fillStyle = radgrad3;
ctx.fillRect(0,0,150,150);
ctx.fillStyle = radgrad2;
ctx.fillRect(0,0,150,150);
ctx.fillStyle = radgrad;
ctx.fillRect(0,0,150,150);
```
<img src="./images/clip.jpg" >

## 动画
```
window.requestAnimationFrame(callback);
window.cancelAnimationFrame();
```
## 像素操作
到目前为止，我们尚未深入了解Canvas画布真实像素的原理，事实上，你可以直接通过ImageData对象操纵像素数据，直接读取或将数据数组写入该对象中。稍后我们也将深入了解如何控制图像使其平滑（反锯齿）以及如何从Canvas画布中保存图像  
#### ImageData对象
ImageData对象中存储着canvas对象真实的像素数据，它包含以下几个只读属性：  
__width__  
图片宽度，单位是像素  
__height__  
图片高度，单位是像素  
__data__  
Uint8ClampedArray类型的一维数组，包含着RGBA格式的整型数据，范围在0至255之间（包括255）。  
例如，要读取图片中位于第50行，第200列的像素的蓝色部份，你会写以下代码：  
```
blueComponent = imageData.data[((50 * (imageData.width * 4)) + (200 * 4)) + 2];
```
根据行、列读取某像素点的R/G/B/A值的公式：  
```
imageData.data[((50 * (imageData.width * 4)) + (200 * 4)) + 0/1/2/3];
```
你可能用会使用Uint8ClampedArray.length属性来读取像素数组的大小（以bytes为单位）：  
```
var numBytes = imageData.data.length;
```
#### 创建一个ImageData对象
去创建一个新的，空白的ImageData对象，你应该会使用createImageData() 方法。有2个版本的createImageData()方法。  
```
var myImageData = ctx.createImageData(width, height);
```
上面代码创建了一个新的具体特定尺寸的ImageData对象。所有像素被预设为透明黑。  
你也可以创建一个被anotherImageData对象指定的相同像素的ImageData对象。这个新的对象像素全部被预设为透明黑。这个并非复制了图片数据  
```
var myImageData = ctx.createImageData(anotherImageData);
```
#### 得到场景像素数据
为了获得一个包含画布场景像素数据的ImageData对像，你可以用getImageData()方法：  
```
var myImageData = ctx.getImageData(left, top, width, height);
```
这个方法会返回一个ImageData对象，它代表了画布区域的对象数据，此画布的四个角落分别表示为(left, top), (left + width, top), (left, top + height), 以及(left + width, top + height)四个点。这些坐标点被设定为画布坐标空间元素。  
> __注__ ：任何在画布以外的元素都会被返回成一个透明黑的ImageData对像。
#### 在场景中写入像素数据
你可以用putImageData()方法去对场景进行像素数据的写入。  
```
ctx.putImageData(myImageData, dx, dy);
```
dx和dy参数表示你希望在场景内左上角绘制的像素数据所得到的设备坐标。  
#### 缩放和反锯齿
在drawImage() 方法， 第二个画布和imageSmoothingEnabled 属性的帮助下，我们可以放大显示我们的图片及看到详情内容。  
因为反锯齿默认是启用的，我们可能想要关闭它以看到清楚的像素。你可以通过切换勾选框来看到imageSmoothingEnabled属性的效果（不同浏览器需要不同前缀）。  
```
var img = new Image();
img.src = 'https://mdn.mozillademos.org/files/5397/rhino.jpg';
img.onload = function() {
  draw(this);
};

function draw(img) {
  var canvas = document.getElementById('canvas');
  var ctx = canvas.getContext('2d');
  ctx.drawImage(img, 0, 0);
  img.style.display = 'none';
  var zoomctx = document.getElementById('zoom').getContext('2d');

  var smoothbtn = document.getElementById('smoothbtn');
  var toggleSmoothing = function(event) {
    zoomctx.imageSmoothingEnabled = this.checked;
    zoomctx.mozImageSmoothingEnabled = this.checked;
    zoomctx.webkitImageSmoothingEnabled = this.checked;
    zoomctx.msImageSmoothingEnabled = this.checked;
  };
  smoothbtn.addEventListener('change', toggleSmoothing);

  var zoom = function(event) {
    var x = event.layerX;
    var y = event.layerY;
    zoomctx.drawImage(canvas,
                      Math.abs(x - 5),
                      Math.abs(y - 5),
                      10, 10,
                      0, 0,
                      200, 200);
  };

  canvas.addEventListener('mousemove', zoom);
}
```
#### 保存图片
HTMLCanvasElement  提供一个toDataURL()方法，此方法在保存图片的时候非常有用。它返回一个包含被类型参数规定的图像表现格式的数据链接。返回的图片分辨率是96dpi。  
__canvas.toDataURL('image/png')__  
默认设定。创建一个PNG图片  
__canvas.toDataURL('image/jpeg', quality)__  
创建一个JPG图片。你可以有选择地提供从0到1的品质量，1表示最好品质，0基本不被辨析但有比较小的文件大小。  
你也可以从画布中创建一个Blob对像  
__canvas.toBlob(callback, type, encoderOptions)__  
这个创建了一个在画布中的代表图片的Blob对像  