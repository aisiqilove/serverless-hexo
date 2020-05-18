---
title: JS讲图片转为base64并压缩
date: 2019-03-24 19:57
tags: 
- base64
- Img
- 压缩
---

``` js
addImg(event) {
    var imgFile = event.target.files[0];
    //判断类型是不是图片
    if (!/image\/\w+/.test(imgFile.type)) {
        alert("请确保文件为图像类型");
        return;
    }
    //通过html5 特性fileReader转换base64  通过compressImg方法进行压缩
    var reader = new FileReader();
    reader.readAsDataURL(imgFile);
    const that = this;
    reader.onloadend = function() { //文件读取结束的时候上传到服务器
        that.results = reader.result;
        that.$vux.loading.show({
            text: '上传中'
        })
        var img = document.querySelector("#img");
        img.src = reader.result;
        var width = img.width;
        var height = img.height;
        var result = compressImg(img, width, height);

        that.$emit('sendImgInfo', result); //将上传的图片以base64形式传给父组件
    }
}

/**
 * 图片压缩
 */
export const compressImg = (img, width, height) => {
    if (img.src.length < 2000000) { //小于200K的压缩
        return img.src;
    }
    //用于压缩图片的canvas
    console.log(width + "," + height)
    let canvas = document.createElement("canvas");
    let ctx = canvas.getContext('2d');

    // 瓦片canvas
    // var tCanvas = document.createElement("canvas");
    // var tctx = tCanvas.getContext("2d");

    let initSize = img.src.length;
    // console.log(img.src)

    var ratio;
    if ((ratio = width * height / 4000000) > 1) {
        ratio *= 10;

        width /= ratio;
        height /= ratio;
    } else {
        ratio = 5;
    }
    console.log('ratio=' + ratio)
    canvas.width = width * 2;
    console.log(width)
    canvas.height = height * 2;
    //铺底色
    ctx.fillStyle = "#fff";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    //如果图片像素大于100万则使用瓦片绘制
    // var count;
    // if ((count = width * height / 1000000) > 1) {
    // console.log('count='+count)
    // count = ~~(Math.sqrt(count) + 1); //计算要分成多少块瓦片
    // //计算每块瓦片的宽和高
    // var nw = ~~(width / count);
    // var nh = ~~(height / count);
    // tCanvas.width = nw;
    // tCanvas.height = nh;
    // for (var i = 0; i < count; i++) {
    // for (var j = 0; j < count; j++) {
    // tctx.drawImage(img, i * nw * ratio, j * nh * ratio, nw * ratio * 2, nh * ratio * 2, 0, 0, nw, nh);
    // ctx.drawImage(tCanvas, i * nw, j * nh, nw * 2, nh * 2);
    // }
    // }
    // } else {
    ctx.drawImage(img, 0, 0, width * 2, height * 2);
    // }
    //进行最小压缩
    let dataURL = canvas.toDataURL('image/jpeg', 1);
    console.log('压缩前：' + initSize);
    console.log('压缩后：' + dataURL.length);
    console.log('压缩率：' + ~~(100 * (initSize - dataURL.length) / initSize) + "%");
    console.log(dataURL)
    return dataURL;
}
```
