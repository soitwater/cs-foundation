# String
```js
 'abc'.charAt(1)	
 'abc'.charCodeAt(1)	返回'b'的字符编码（'a'的字符编码是97）
 'abc'.slice(0,2)	裁剪，同数组的操作方式 (同`substr(start, length)`(按长度截) `substring(start, index)`(按索引截取))
 'abc'.indexOf('a')	
 'abc'.lastIndexOf('a')	
 'abc'.trim()	
 'abc'.replace('a', 'z')	也用来在指定位置插入/删除
 'abc'.split('')	
 'ABC'.toLowerCase()	
 'abc'.toUpperCase()	
 'a'.repeat(3) 	字符a重复3次

a.sort(function(a,b){return a.localeCompare(b)}); //正序排列 - 字符串排序
a.sort(function(a,b){return b.localeCompare(a)}); //反序排列	

string.localeCompare(target)	
参数target是要与string进行比较的字符串。	
如果string小于target，则localeCompare()返回小于0的数；	
如果string大于target，返回大于0的数；	

中文排序时  可以通过加参数的方法  a.localeCompare(b,'zh-CN')
```