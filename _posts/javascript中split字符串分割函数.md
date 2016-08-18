title: javascript中split字符串分割函数
date: 2016-01-14 15:55:27
tags: "js"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
    
<blockquote class="blockquote-center">这是一个头样式 --2016/1/14</blockquote>
假设需要分割的字符串是：s="....fs...fs....",其中fs代表用以分隔的字符或字符串。
## 定义和用法

> split() 方法用于把一个字符串分割成字符串数组。


## 语法

> stringObject.split(separator,howmany)

### 实例一
```javascript
<script type="text/javascript">
    var ss=s.split("fs");
    for(var i=0;i<ss.length;i++){
        //处理每一个ss[i];
    }
</script>    
```
### 实例二
<p>在本例中，我们将分割结构更为`复杂`的字符串：</p>
```javascript
    "2:3:4:5".split(":") //将返回["2", "3", "4", "5"]
    "|a|b|c".split("|") //将返回["", "a", "b", "c"]
```
### 实例三
```javascript
<script type="text/javascript">
    var str = "一二三四";
    var str1 = "篮球、排球、乒乓球";
    var arr = str.split("");//全部分割
    var arr1 = str1.split("、");//按照顿号分割
    var arr2 = str1.split("、",2);//按照顿号分割,保留两段
</script>    
```
大家可以在本地测试一下

### 实例四
```html
<input id="x" type="text"/>
<input id="x" type="text"/><input type="button" onclick="x()" value="输入邮件地址，获取用户名"/>
<script type="text/javascript">
```
```javascript
<script type="text/javascript">
function x(){
    var x=document.getElementById("x").value.toString();
    var c=x.split("@");
    document.getElementById("x").value=c[0];
}
</script>
```
注释：如果把空字符串 ("") 用作 separator，那么 stringObject 中的每个字符之间都会被分割。

总结:split函数很像我们以前学的php和asp中的字符分割函数，它只要以什么作分割线就可以把我们要的内容分割成数组了。
