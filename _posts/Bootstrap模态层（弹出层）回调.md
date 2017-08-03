title: Bootstrap模态层（弹出层）回调
date: 2016-01-15 17:33:03
tags: "bootstrap"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
# 项目实例：
```javascript
// 执行关闭事件（hide.bs.modal未完全关闭前） hidden.bs.modal 为完全关闭后
$("#myModal").on('hidden.bs.modal',function (){
	// 清除播放录音
	$(".bf_content").empty('');
})
```

## 事件 描述 实例
```javascript

//show.bs.modal 在调用 show 方法后触发。
$('#identifier').on('show.bs.modal', function () {
  // 执行一些动作...
})

//shown.bs.modal 当模态框对用户可见时触发（将等待 CSS 过渡效果完成）。	
$('#identifier').on('shown.bs.modal', function () {
  // 执行一些动作...
})

//hide.bs.modal	当调用 hide 实例方法时触发。
$('#identifier').on('hide.bs.modal', function () {
  // 执行一些动作...
})

//hidden.bs.modal 当模态框完全对用户隐藏时触发。	
$('#identifier').on('hidden.bs.modal', function () {
  // 执行一些动作...
})
```