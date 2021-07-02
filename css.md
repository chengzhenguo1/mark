##### 使用margin-left排版左重右轻列表

- 要点：使用`flex横向布局`时，最后一个元素通过`margin-left:auto`实现向右对齐
- 场景：**右侧带图标的导航栏**
- 兼容：[margin](https://caniuse.com/#search=margin)
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/PoYpROw)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8margin-left%E6%8E%92%E7%89%88%E5%B7%A6%E9%87%8D%E5%8F%B3%E8%BD%BB%E5%88%97%E8%A1%A8.png)

```` html
<style>
.highlight-list {
	display: flex;
	align-items: center;
	padding: 0 10px;
	width: 600px;
	height: 60px;
	background-color: #3c9;
	& + .highlight-list {
		margin-top: 10px;
	}
	li {
		padding: 0 10px;
		height: 40px;
		background-color: #3c9;
		line-height: 40px;
		font-size: 16px;
		color: #fff;
	}
	&.left li {
		& + li {
			margin-left: 10px;
		}
		&:last-child {
			margin-left: auto;
		}
	}
	&.right li {
		& + li {
			margin-left: 10px;
		}
		&:first-child {
			margin-right: auto;
		}
	}
}
</style>
<div class="bruce flex-ct-y" data-title="使用margin排版凸显布局">
	<ul class="highlight-list left">
		<li>Alibaba</li>
		<li>Tencent</li>
		<li>Baidu</li>
		<li>Jingdong</li>
		<li>Ant</li>
		<li>Netease</li>
	</ul>
	<ul class="highlight-list right">
		<li>Alibaba</li>
		<li>Tencent</li>
		<li>Baidu</li>
		<li>Jingdong</li>
		<li>Ant</li>
		<li>Netease</li>
	</ul>
</div>

````

##### 使用attr()抓取data-*

- 要点：在标签上自定义属性`data-*`，通过`attr()`获取其内容赋值到`content`上
- 场景：**提示框**
- 兼容：[data-*](https://caniuse.com/#search=data-)、[attr()](https://caniuse.com/#search=attr())
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/voRdKX)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8attr%E6%8A%93%E5%8F%96data-.gif)

```` html
 <style>
     .title {
         position: relative;
         width: 100px;
         text-align: center;
     }
     .title::after {
         position: absolute;
         content: attr(data-msg);
         top: 0;
         left: 100px;
         background-color: rgba(0,0,0, .5);
         text-align: center;
         font-size: 12px;
         transition: all 300ms;
         opacity: 0;
     }
     .title:hover::after {
         opacity: 1;
     }
</style>
<div class="title" data-msg='helloWorld'>title</div>

````

##### 使用:valid和:invalid校验表单

- 要点：`<input>`使用伪类`:valid`和`:invalid`配合`pattern`校验表单输入的内容
- 场景：**表单校验**
- 兼容：[pattern](https://caniuse.com/#search=pattern)、[:valid](https://caniuse.com/#search=%3Avalid)、[:invalid](https://caniuse.com/#search=%3Ainvalid)
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/QemxKr)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8valid%E5%92%8Cinvalid%E6%A0%A1%E9%AA%8C%E8%A1%A8%E5%8D%95.gif)

##### 使用pointer-events禁用事件触发

- 要点：通过`pointer-events:none`禁用事件触发(默认事件、冒泡事件、鼠标事件、键盘事件等)，相当于`<button>`的`disabled`
- 场景：**限时点击按钮**(发送验证码倒计时)、**事件冒泡禁用**(多个元素重叠且自带事件、a标签跳转)
- 兼容：[pointer-events](https://caniuse.com/#search=pointer-events)
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/dxmrLj)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8pointer-events%E7%A6%81%E7%94%A8%E4%BA%8B%E4%BB%B6%E8%A7%A6%E5%8F%91.gif)

##### 使用+或~美化选项框

- 要点：`<label>`使用`+`或`~`配合`for`绑定`radio`或`checkbox`的选择行为
- 场景：**选项框美化**、**选中项增加选中样式**
- 兼容：[+](https://caniuse.com/#search=+)、[~](https://caniuse.com/#search=~)
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/rXdbgZ)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8+%E6%88%96~%E7%BE%8E%E5%8C%96%E9%80%89%E9%A1%B9%E6%A1%86.gif)

##### 使用:focus-within分发冒泡响应

- 要点：表单控件触发`focus`和`blur`事件后往父元素进行冒泡，在父元素上通过`:focus-within`捕获该冒泡事件来设置样式
- 场景：**登录注册弹框**、**表单校验**、[**离屏导航**](https://codepen.io/dannievinther/pen/NvZjvz)、[**导航切换**](https://codepen.io/Chokcoco/pen/RJEpaP)
- 兼容：[:focus-within](https://www.caniuse.com/#search=%3Afocus-within)、[:placeholder-shown](https://www.caniuse.com/#search=%3Aplaceholder-shown)
- 代码：[在线演示](https://codepen.io/JowayYoung/pen/BaBjaBP)
- [神奇的选择器 :focus-within - ChokCoco - 博客园 (cnblogs.com)](https://www.cnblogs.com/coco1s/p/9406413.html)



![在线演示](https://yangzw.vip/static/article/css-skill/%E4%BD%BF%E7%94%A8focus-within%E5%88%86%E5%8F%91%E5%86%92%E6%B3%A1%E5%93%8D%E5%BA%94.gif)

##### 使用max-height动态高度过渡动画

 **CSS transtion 不支持元素的高度或者宽度为 auto 的变化**

这里有一个非常有意思的小技巧。既然不支持 `height: auto`，那我们就另辟蹊径，利用 `max-height` 的特性来做到动态高度的伸缩，譬如：

```CSS
{
    max-height: 0;
    transition: max-height 0.3s linear;

    &.up {
        max-height: 0;
    }
    &.down {
        max-height: 1000px;
    }
}
复制代码
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4a52e6fbf9b4e66bcaa8b71996243c2~tplv-k3u1fbpfcp-watermark.image)

具体的详情你可以看看 -- [CSS 奇技淫巧：动态高度过渡动画](https://github.com/chokcoco/iCSS/issues/91)。

##### 浪文本网页特效

利用css变量设置动画延迟时间

```` html
<div class="wrapper">
        <!-- css变量 -->
        <span style="--i:1">欢</span>
        <span style="--i:2">迎</span>
        <span style="--i:3">来</span>
        <span style="--i:4">到</span>
        <span style="--i:5">CSS</span>
        <span style="--i:6">世</span>
        <span style="--i:7">界</span>
</div>
````

```` css
.wrapper {
    display: flex;
    /* 倒影 */
    -webkit-box-reflect: below -12px linear-gradient(transparent, rgba(0, 0, 0, 0.2));
}

.wrapper span {
    font-size: 2em;
    animation: animate 1s ease-in-out infinite;
    color: #fff;
    /* 延迟时间 */
    animation-delay: calc(.1s * var(--i));
}

@keyframes animate {
    0% {
        transform: translateY(0);
    }
    20% {
        transform: translateY(-24px);
    }
    40%,
    100% {
        transform: translateX(0);
    }
}
````

