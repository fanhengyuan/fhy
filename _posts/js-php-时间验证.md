title: 'js&php 时间验证'
date: 2016-01-18 19:27:05
tags: "php"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
  
# php

```php
//长时间，形如 (2016-01-18 13:04:06)
function isDateTime($str)
{
    //$matches = Array ( [0] => 2003-12-05 20:2:28 [1] => 2003 [2] => - [3] => 12 [4] => 05 [5] => 20 [6] => :[7] => 2 [8] => 28 );
    $s = preg_match('/^(\d{1,4})(-|\/)(\d{1,2})\2(\d{1,2}) (\d{1,2})(:)?(\d{1,2})\6(\d{1,2})$/', $str, $matches);

    if(empty($s))return False;
    if(False === checkdate ($matches[3], $matches[4], $matches[1]))return False; 
    if($matches[5]>24 || $matches[7]>60 || $matches[8]>60)return False;
    return sprintf("%04d-%02d-%02d %02d:%02d:%02d", $matches[1], $matches[3], $matches[4], $matches[5], $matches[7],$matches[8]);
}
```
调用方式：isDateTime(时间串)
返回FALSE说明格式/日期不正确
否则返回格式化过的标准时间传 
***
# js

```javascript
<script type="text/javascript">
String.prototype.isTime = function()
{
  var r = this.match(/^(\d{1,4})(-|\/)(\d{1,2})\2(\d{1,2}) (\d{1,2}):(\d{1,2}):(\d{1,2})$/); 
  if(r == null)
  {
  	return false;
  }
  var d = new Date(r[1], r[3]-1,r[4],r[5],r[6],r[7]); 
  return (d.getFullYear()==r[1]&&(d.getMonth()+1)==r[3]&&d.getDate()==r[4]&&d.getHours()==r[5]&&d.getMinutes()==r[6]&&d.getSeconds()==r[7]);
}
alert("2015-1-31 12:34:56".isTime());
alert("2016-1-18 12:54:56".isTime());
alert("2002-1-41 12:00:00".isTime());
</script>
```
