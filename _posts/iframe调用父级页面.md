title: iframe调用父级页面
date: 2016-03-03 19:22:11
tags: "js"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>

```javascript
// 调用父级页面的moniclick方法
window.parent.moniclick(baoming_id);
//fancybox关闭
parent.jQuery.fancybox.close();
//刷新父级页面
window.parent.location.reload();
```

父页面方法
> window.parent.settdly($("#baoming_id").val(),td_liyou);   

```javascript 
  //提交表单
  $("#btn_sub").on('click',function(){
    var td_liyou = $("#td_ly").val();
    if(td_liyou != null){            
      var trimtd_liyou = td_liyou.replace(/(^\s*)|(\s*$)/g, "");
      var ly_len = trimtd_liyou.length;
      if(ly_len<1){
          alert("对不起，退单理由不能为空");
          return false;
      }
    } else{
          return false;
    }    
    window.parent.settdly($("#baoming_id").val(),td_liyou);    
    // parent.settdly($("#baoming_id").val(),td_liyou);
    // $("#btn_sub").attr('disabled','disabled'); //添加disabled属性 谷歌不支持自动结束
    $("#btn_sub").html('正在提交,请等待...');  
  })
```