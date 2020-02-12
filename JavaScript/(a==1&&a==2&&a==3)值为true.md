# (a ==1 && a== 2 && a==3) 值为true
什么情况下 (a ==1 && a== 2 && a==3) 值为true 成立  
&nbsp;  
## 解法1
```js
const a = {
  i: 1,
  toString: function () {
    return a.i++;
  }
}
if (a == 1 && a == 2 && a == 3) {
  console.log('Hello World!')
}
```
## 解法2
```js
var b = 0;
var a = {
  valueOf () {
	  return b += 1;
}}
if (a == 1 && a == 2 && a == 3) {
	console.log('hello')
}
```
## 解法3
```js
var val = 0;
Object.defineProperty(window, 'a', {
  get: function () {
    return ++val;
  }
});
if (a == 1 && a == 2 && a == 3) {
  console.log('yay')
}
```
