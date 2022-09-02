# h5键盘弹起遮挡输入框

```js
window.addEventListener('resize', function () {
    if (
        document.activeElement.tagName === 'InputItem' ||
        document.activeElement.tagName === 'textarea'
    ) {
        window.setTimeout(function () {
            if ('scrollIntoView' in document.activeElement) {
                document.activeElement.scrollIntoView()
            } else {
                document.activeElement.scrollIntoViewIfNeeded()
            }
        }, 0)
    }
})
```