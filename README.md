# js-poster
纯 JS 生成带二维码海报

## 特点
1. 简单：算法不到100行代码轻松，轻松可看懂。
2. 强大：支持远程图片，再大的远程图片也不会出现白屏。
3. 兼容性好：除引用 qrcode 库外，不使用任何第三方框架，纯JS实现，测试未遇到过生成不了的场景。

## 使用方法
请查看 index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js-poster JS 海报生成示例</title>
    <script src="qrcode.min.js"></script>
</head>
<body>
<img id="poster">
<canvas id="myCanvas" class="poster" style="display: none"></canvas>
<div id="qrcode" ref="qrcode" style="display: none"></div>

<script type="application/javascript">
    const backgroundUrl = 'background.jpg';  //可设置远程图片地址
    const avatarUrl = 'avatar.jpg';     //可设置远程图片地址
    const nickName = "纯 JS 的海报生成，支持远程图片";

    window.onload=function(){
        creatQRcode()
    }
    function creatQRcode() {
        var qrcode = new QRCode('qrcode', {
            text: "纯 JS 的海报生成，支持远程图片",
            width: 150, //图像宽度
            height: 150, //图像高度
            colorDark: "#000000", //前景色
            colorLight: "#ffffff", //背景色
            typeNumber: -1,
            correctLevel: QRCode.CorrectLevel.H
        });
        setTimeout(()=>{
            createPoster();
        },200)

    }
    function createPoster(){
        console.log("开始画")
        let bgImage = new Image();
        let avatarImage = new Image();

        Promise.all([
            downloadImage(bgImage,backgroundUrl),
            downloadImage(avatarImage,avatarUrl)
        ]).then(()=>{
            console.log("完成了下载")
            console.log("背景",bgImage)
            console.log("头像",avatarImage)
            var imgQrcode = document.getElementById('qrcode').getElementsByTagName('img')[0];
            var can = document.getElementById('myCanvas');

            can.setAttribute("crossOrigin",'Anonymous');
            let ctx = can.getContext("2d");

            can.width = 1000;
            can.height = 1500;
            // 背景图片
            ctx.drawImage(bgImage, 0, 0, 1000, 1500);
            // 头像
            ctx.drawImage(avatarImage, 450, 50, 200, 200);
            // 二维码
            ctx.drawImage(imgQrcode, 400, 300 , 300 , 300 );

            //绘制昵称
            ctx.font = 'bold 36px Arial';
            ctx.fillStyle = "#1F7EFF";
            ctx.fillText(nickName, 300 , 1400 , 500 );

            // canvas转换为base64
            var base64 =""
            try {
                base64 = can.toDataURL();
            } catch (err) {
                alert(err)
            }
            document.getElementById('poster').src = base64;

        })
    }
    function downloadImage(image,url){
        image.setAttribute("crossOrigin", "anonymous");
        image.src = url
        return new Promise((resolve, reject) => {
            image.onload = function() {
                resolve(image)
            };
        })
    }
</script>
</body>
</html>

```
