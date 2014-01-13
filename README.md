##fis-spriter-csssprites
[![NPM version](https://badge.fury.io/js/fis-spriter-csssprites.png)](http://badge.fury.io/js/fis-spriter-csssprites)

基于FIS的csssprites，对css文件以文件为级别进行csssprites处理。支持`repeat-x`, `repeat-y`, `background-position`

###安装

```bash
$ npm install -g fis-spriter-csssprites
```

####环境要求
0. 依赖native插件，[node-images](https://github.com/xiangshouding/node-images) 环境需要符合个插件的要求。(OS X、Windows、Linux x86 提供了二进制包)
0. 只能在FIS中使用

###配置

```javascript
fis.config.merge({
    namespace: 'demo',
    modules: {
        spriter: 'csssprites'
    },
    roadmap: {
        path: {
            reg: /\/static\/.*\.css$/i,
            //配置useSprite表示reg匹配到的css需要进行图片合并
            useSprite: true
        }
    },
    pack: {
        //对合并的aio.css进行处理
        'aio.css': [
            '**.css'
        ]
    },
    settings: {
        spriter: {
            csssprites: {
                //图之间的边距
                margin: 10,
                //使用矩阵排列方式，默认为线性`linear`
                layout: 'matrix' 
            }
        }
    }
});

```

###使用
调用执行spriter，需要`fis release`时加`-p`参数: `fis release -p`，具体请[参照文档](https://github.com/fis-dev/fis/wiki/%E9%85%8D%E7%BD%AEAPI#modulesspriter)

在书写css时标注`background-image`的图片是否进行合并，标注说明；

![background](https://raw.github.com/xiangshouding/fis-spriter-csssprites/master/doc/image/background.png)

通过图片添加query识别图片是否需要做图片合并，具体

<table>
    <tr>
        <th>query</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>?__sprite</td>
        <td>标识图片要做合并</td>
    </tr>
</table>

支持图片的background-position：有的情况下引用的图片已经是合并了几个小图的图片，通过`background-position`来显示每个小图，这种情况也是支持的。

支持以下几种`background-position`，有的同学不知道`background-position`是如何工作的，请参见[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-position)

|支持写法|示例|使用场景|
|:------|:----|:--------|
|background-position: \d+px \d+px;|background-position: -9px -1px;|需要合并的图是一个合并了很多小图的图片|
|background-position: left  \d+px;|background-position: left -11px;|需要合并的图片向左浮动|
|background-position: right \d+px;|background-position: right -1px;|需要合并的图片向右浮动|
|background-position: left top;|background-position: left top;|需要合并的图片向左浮动|
|background-position: right top;|background-position: right top;|需要合并的图片向右浮动

###示例

源代码: `aio.css`

```css
.header, .footer {
    background: url(/img/1px_bg.png?__sprite) repeat-x;
    height: 150px;
    width: 960px;
}

.nav {
    min-height: 400px;
    width: 100px;
    background: url(/img/nav_bg.png?__sprite) repeat-y;
}

.icon_add {
    width: 25px;
    height: 25px;
    background: url(/img/icon/add.jpg?__sprite) no-repeat;
}

.icon_mul {
    width: 25px;
    height: 25px;
    background: url(/img/icon/mul.jpg?__sprite) no-repeat;
}
```
产出结果：

```css
.header, .footer {
    height: 150px;
    width: 960px;
    background-repeat: repeat-x
}

.nav {
    min-height: 400px;
    width: 100px;
    background-repeat: repeat-y
}

.icon_add {
    width: 25px;
    height: 25px;
    background-repeat: no-repeat
}

.icon_mul {
    width: 25px;
    height: 25px;
    background-repeat: no-repeat
}

.header, .footer {
    background-position: 0px 0px;
}

.nav {
    background-position: 0px 0px;
}

.icon_add {
    background-position: 0px 0px;
}

.icon_mul {
    background-position: 0px -25px;
}

.header, .footer {
    background-image: url('aio_x.png');
}

.nav {
    background-image: url('aio_y.png');
}

.icon_add, .icon_mul {
    background-image: url('aio_z.png');
}

```

如上，`1px_bg.png`会合并到`aio_x.png`(aio.css对应图片), `nav_bg.png`合并到`aio_y.png`, `add.jpg`和`mul.jpg`被合并到`aio_z.png`。

###其他
[实现原理](https://github.com/xiangshouding/fis-spriter-csssprites/wiki/CssSprites%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)
