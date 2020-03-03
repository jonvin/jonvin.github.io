---
title: '[vue中使用画布生成图片]'
date: 2020-02-25 09:33:22
tags:
---
说明：创建一个canvas后，为什么style、width、height需要设置为绑定状态？因为画布的width和height是int类型，默认单位为px，在本项目中使用vw单位来实现UI和适配各种机型，所以需要做一个px到vw的转化，在这里设置的width是80vw，height是80vw，unit是绑定变量，后面会提到。style属性设置为绑定状态，是因为canvas在flex布局中的宽度默认是100%，并且不受width属性影响，所以需要在这里指定style中的width和height，和属性width、height值同步canvas和div#qrcode中都有hidden属性，都是用于生产海报图片的，海报图片显示在div#img_box中， 所以他们都是隐藏的


### template部分

```html
<template>
    <div class="container">
        <canvas id="canvas" class="canvas" :style="canvasStyle" :width="80*unit" :height="80*unit" hidden></canvas>
        <div id="qrcode" hidden></div>
        <div id="img_box"></div>
    </div>
</template>
```

### script部分

```javascript
let ctx;
export default {
    name: "Index",
    data() {
        return {
            unit: document.body.clientWidth/100, // 将px转撑px unit = vw
            canvasStyle: "width:" + document.body.clientWidth*.8 + "px;" + "height:" + document.body.clientWidth*.8 + "px;", // 构建canvas样式
        };
    },
    mounted() {
        let canvas = document.querySelector('#canvas'); // 获取画布实例
        ctx = canvas.getContext('2d'); 
        this.createImg();
    },
    methods: {
        createImg() {
            this.drawBg();
        },
        // 绘制背景
        drawBg() {
            ctx.fillStyle = 'pink';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            this.drawAvatar(require('../assets/images/avatar.jpg'), 60, 60, 30); // 头像地址，头像位置x, y, 头像半径
        },
        // 绘制头像
        drawAvatar(url, x, y, r) {
            let img = new Image();
            img.src = url;
            img.onload = () => {
                ctx.save();
                ctx.arc(x+r, y+r, r, 0, 2 * Math.PI); // 绘制圆角头像
                ctx.clip();
                ctx.drawImage(img, x, y, r*2, r*2);
                ctx.restore();
                this.drawQrCode();
            }
        },
        // 绘制二维码
        drawQrCode() {
            // QRCode 类是引入的库，在vue项目index.html中引入库文件，库文件详情：https://code.ciaoca.com/javascript/qrcode/
            // 在选择节点中插入二维码图片
            let qrcode = new QRCode(document.querySelector('#qrcode'), {
                text: 'https://baidu.com',
                width: 256,
                height: 256,
                colorDark : '#000000',
                colorLight : '#ffffff',
                correctLevel : QRCode.CorrectLevel.H
            });
            let qrcodeImg = document.querySelector('#qrcode img'); //获取二维码图片节点
            qrcodeImg.onload = () => {
                ctx.drawImage(qrcodeImg, 240, 240, 60, 60); // 在画布中绘制二维码，参数：二维码图片，二维码位置，二维码宽高
                this.drawFont();
            }
        },
        // 绘制文字
        drawFont() {
            ctx.fillStyle = "red";
            ctx.font = "20px '微软雅黑'";
            ctx.textAlign = "left";
            ctx.shadowBlur = 10;
            ctx.shadowOffsetX = 5;
            ctx.shadowOffsetY = 5;
            ctx.shadowColor = "black";
            ctx.fillText("You jump! I jump!", 100, 180);
            let dataImg = new Image();
            dataImg.src = canvas.toDataURL('image/png'); // 画布转图片
            document.querySelector('#img_box').appendChild(dataImg); // 在选中节点中插入图片
        }
    }
};
```

### 全部代码

```vue
<template>
    <div class="container">
        <canvas id="canvas" class="canvas" :style="canvasStyle" :width="80*unit" :height="80*unit" hidden></canvas>
        <div id="qrcode" hidden></div>
        <div id="img_box"></div>
    </div>
</template>
<script>
let ctx;
export default {
    name: "Index",
    data() {
        return {
        unit: document.body.clientWidth/100,
        canvasStyle: "width:" + document.body.clientWidth*.8 + "px;" + "height:" + document.body.clientWidth*.8 + "px;",
        };
    },
    mounted() {
        let canvas = document.querySelector('#canvas');
        ctx = canvas.getContext('2d');
        this.createImg();
    },
    methods: {
        createImg() {
            this.drawBg();
        },
        // 绘制背景
        drawBg() {
            ctx.fillStyle = 'pink';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            this.drawAvatar(require('../assets/images/avatar.jpg'), 60, 60, 30);
        },
        // 绘制头像
        drawAvatar(url, x, y, r) {
            let img = new Image();
            img.src = url;
            img.onload = () => {
                ctx.save();
                ctx.arc(x+r, y+r, r, 0, 2 * Math.PI);
                ctx.clip();
                ctx.drawImage(img, x, y, r*2, r*2);
                ctx.restore();
                this.drawQrCode();
            }
        },
        // 绘制二维码
        drawQrCode() {
            let qrcode = new QRCode(document.querySelector('#qrcode'), {
                text: 'https://baidu.com',
                width: 256,
                height: 256,
                colorDark : '#000000',
                colorLight : '#ffffff',
                correctLevel : QRCode.CorrectLevel.H
            });
            let qrcodeImg = document.querySelector('#qrcode img');
            qrcodeImg.onload = () => {
                ctx.drawImage(qrcodeImg, 240, 240, 60, 60);
                this.drawFont();
            }
        },
        // 绘制文字
        drawFont() {
            ctx.fillStyle = "red";
            ctx.font = "20px '微软雅黑'";
            ctx.textAlign = "left";
            ctx.shadowBlur = 10;
            ctx.shadowOffsetX = 5;
            ctx.shadowOffsetY = 5;
            ctx.shadowColor = "black";
            ctx.fillText("You jump! I jump!", 100, 180);
            let dataImg = new Image();
            dataImg.src = canvas.toDataURL('image/png');
            document.querySelector('#img_box').appendChild(dataImg);
        }
    }
};
</script>
<style scoped>
.container {
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: start;
}
.canvas {
    margin: 5vw auto;
    background-color: pink;
}
#img_box {
    margin: 5vw auto;
}
</style>
```