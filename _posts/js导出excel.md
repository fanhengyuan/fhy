title: js导出excel
date: 2016-08-09 18:13:32
tags: "js"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>

> 参考网上代码

```html
    <div style="margin:15px;" id="kaoqin" >
        <form type="get" action="" id="uploadForm" enctype="multipart/form-data">
            <table class="listing">
                <tr>
                    <th>请选择原始数据</th>
                    <td>
                        <input id="file" type="file" name="file"/>
                    </td>
                    <td>
                        <button id="upload" type="button">预览</button>
                    </td>
                    <td>
                        <button id="export" type="button">导出</button>
                    </td>                
                </tr>
            </table>        
        </form>
        <table id="tableExcel" width="90%" border="1" cellspacing="0" cellpadding="0"></table>
    </div>
```

```javascript
    <script type="text/javascript">
        var lock = true;
        $("#upload").on('click', function(){
            if(!lock){alert('请勿重复提交！'); return false;}
            if(!confirm("是否确认转换？")){return false;}        
            $.ajax({
                url: '/index/ajax_input_out_to_set',
                type: 'POST',
                cache: false,
                data: new FormData($('#uploadForm')[0]),
                processData: false,
                contentType: false,
                beforeSend:function(){      
                    lock = false;       
                    $("#tableExcel").html('加载中...');
                },            
            }).done(function(res) {
                $("#tableExcel").html(res);
                if(res.indexOf("not readable") >= 0){
                    return false;
                }
            }).fail(function(res) {
                alert(res.status);
                // for (r in res){
                //     alert(r)
                // }
            });
        })

        // 导出
        $("#export").on('click', function(){
            var strr = $("#tableExcel").html();
            if(strr == ''){
                alert('请先点击预览！');
                return false;            
            }
            if(strr.indexOf("not readable") >= 0){
                alert('Excel内容错误！');
                return false;
            }

            // 获取上传文件名
            var file = $("#file").val();
            var strFileName = getFileName(file);
            var str_arr = new Array(); //定义一数组
            str_arr = strFileName.split("."); //字符分割 
            method5(tableExcel, str_arr[0]);        
        })

        var idTmr;  
        function  getExplorer() {  
            var explorer = window.navigator.userAgent ; 
            //ie  
            if (explorer.indexOf("MSIE") >= 0) {  
                return 'ie';  
            }  
            //firefox  
            else if (explorer.indexOf("Firefox") >= 0) {  
                return 'Firefox';  
            }  
            //Chrome  
            else if(explorer.indexOf("Chrome") >= 0){  
                return 'Chrome';  
            }  
            //Opera  
            else if(explorer.indexOf("Opera") >= 0){  
                return 'Opera';  
            }  
            //Safari  
            else if(explorer.indexOf("Safari") >= 0){  
                return 'Safari';  
            }
            else if(explorer.indexOf(".NET") >= 0){
                return 'ie';
            }  
        }  
        function method5(tableid, filename) {  
            if(getExplorer()=='ie')  
            {  
                var curTbl = document.getElementById(tableid);  
                var oXL = new ActiveXObject("Excel.Application");  
                var oWB = oXL.Workbooks.Add();  
                var xlsheet = oWB.Worksheets(1);  
                var sel = document.body.createTextRange();  
                sel.moveToElementText(curTbl);  
                sel.select();  
                sel.execCommand("Copy");  
                xlsheet.Paste();  
                oXL.Visible = true;  

                try {  
                    var fname = oXL.Application.GetSaveAsFilename("Excel.xls", "Excel Spreadsheets (*.xls), *.xls");
                } catch (e) {  
                    print("Nested catch caught " + e);  
                } finally {  
                    oWB.SaveAs(fname);  
                    oWB.Close(savechanges = false);  
                    oXL.Quit();  
                    oXL = null;  
                    idTmr = window.setInterval("Cleanup();", 1);  
                }  

            }  
            else  
            {  
                tableToExcel(tableid, '', filename)  
            }  
        }  
        function Cleanup() {  
            window.clearInterval(idTmr);  
            CollectGarbage();  
        }  
        var tableToExcel = (function() {  
            var uri = 'data:application/vnd.ms-excel;base64,',  
                template = '<html><head><meta charset="UTF-8"></head><body><table border="1" cellspacing="0" cellpadding="0">{table}</table></body></html>',  
                base64 = function(s) { return window.btoa(unescape(encodeURIComponent(s))) },  
                format = function(s, c) {  
                    return s.replace(/{(\w+)}/g,  
                    function(m, p) { return c[p]; }) 
                }  
            return function(table, name, filename) {  
                if (!table.nodeType) table = document.getElementById(table)  
                var ctx = {worksheet: name || 'Worksheet', table: table.innerHTML}
                // 1
                // window.location.href = uri + base64(format(template, ctx))  

                // 2 动态生成a标签下载
                var downloadLink = document.createElement("a");
                downloadLink.href = uri + base64(format(template, ctx));            
                var myDate = new Date();
                var str1 = filename.split("_")[0].replace(/^[A-Z]{2}/, ''),
                    str2 = myDate.getFullYear(),
                    str3 = parseInt(filename.split("_")[1]);
                // downloadLink.download = "000_2016_5_卡式报表.xls";
                downloadLink.download = str1+"_"+str2+"_"+str3+"_卡式报表"+".xls";
                document.body.appendChild(downloadLink);
                downloadLink.click();
                document.body.removeChild(downloadLink);            
            }  
        })()

        function getFileName(o){
            var pos=o.lastIndexOf("\\");
            return o.substring(pos+1);  
        }

    </script>
```