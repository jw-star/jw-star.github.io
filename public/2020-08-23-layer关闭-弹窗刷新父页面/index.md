# layer关闭弹窗，刷新父页面


## layer关闭弹窗，刷新父页面

```js
//获取index不要写在函数里，
//可能获取不到index
var index = parent.layer.getFrameIndex(window.name);

function closeFram() {
	window.parent.refreshPage()  //调用父页面方法刷新表格
	parent.layer.close(index);  //关闭当前页面，写在最后面
}
```


