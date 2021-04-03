# js数组取值的两种方式


### 问题
```yaml
yData
0: {zyxhId: null, deptNumber: null, …}
1: {zyxhId: null, deptNumber: null, …}
2: {zyxhId: null, deptNumber: null,  …}

```
```js
var  temp= $(this).attr("id");
// var a=  yData[i].temp;  错误用法
var a=  yData[i][temp] ;  正确用法
```
上面的代码看似没问题，.temp数组[i]的对象值，但是取不出来值。
**原因：js会把temp看做 对象的 key 值，对象中根本没有 key为 temp，永远取不出来值**


