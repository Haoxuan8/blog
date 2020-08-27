# html2canvas-将dom保存为图片

## 问题来源

希望在点击保存图片按钮后，能保存下来联系用的二维码。如果只是张单纯的二维码图片的话，直接给 `a` 标签加个二维码的 `src` 和 `download` 属性即可。但是现在希望在保存下来的图片中增加更多的信息，比如用户的名字，用户的头像等，使整张图片内容更加丰富。

## 解决方法

这时候可以用 `html2canvas`，`html2canvas` 可以对浏览器做到像“截屏”一样的操作，但并不是真正的截屏。他是通过把 `dom` 元素转化为 `canvas` 来做到一个类似截屏的操作，所以并不能做到 100% 的一样。

然后用 `toDataUrl` 接口把 `canvas` 数据转化为 base64 格式。之后赋予 `a` 标签 `src` 和 `download` 就可以做到点击保存下载了。

但是还有个问题，需要保存下来的图片其实是不需要显示在页面当中的，也就是说这一块内容的 `visibility: hidden`，这样子直接用 `html2canvas` 只能生成空白的内容。

在 `0.5.0` 版本以上的 `html2canvas` 中提供了 `onclone`，可以对复制的 `dom` 进行操作而不对原始 `dom` 产生任何影响，也就是说在 `onclone` 的回调函数中将需要转化的内容改为 `visibility: visible`。

## 参考代码

```javascript
onBtnClick = (name) => {
  html2canvas(document.getElementById("export-img"), {
    onclone: (doc) => {
      const temp = doc.getElementById("export-img");
      temp.style.visibility = "visible";
    },
  }).then((canvas) => {
    const url = canvas.toDataURL();
    let a = document.createElement("a");
    a.setAttribute("href", url);
    a.setAttribute("download", name);
    a.click();
  })
}
```

## 注意事项

### 跨域图片

相关参数：useCORS, allowTaint

### 移动端图片模糊

相关参数：scale