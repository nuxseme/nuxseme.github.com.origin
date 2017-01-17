---
title: Jquery-File-Upload
categories: js
date: 2016-12-09 14:04:58
---


### 简介

>优秀的开源 文件上传 插件

### 流程

    1 点击上传按钮，添加文件
    2 文件信息数据传递给模板引擎，每个文件渲染一个template-upload 附到files
    3 点击开始上传，获取设定的服务器url等参数 formData 可以设置额外参数
    4 数据提交到服务器 服务器处理返回结果
    5 浏览器获取服务器返回的数据 渲染template-down 

<!--more-->
### Demo
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <link rel="stylesheet" href="../css/bootstrap.min.css">
        <!-- Generic page styles -->
        <link rel="stylesheet" href="../css/style.css">
        <!-- blueimp Gallery styles -->
        <link rel="stylesheet" href="../css/blueimp-gallery.min.css">
        <!-- CSS to style the file input field as button and adjust the Bootstrap progress bars -->
        <link rel="stylesheet" href="../css/jquery.fileupload.css">
        <link rel="stylesheet" href="../css/jquery.fileupload-ui.css">
        <!-- CSS adjustments for browsers with JavaScript disabled -->
        <noscript><link rel="stylesheet" href="../css/jquery.fileupload-noscript.css"></noscript>
        <noscript><link rel="stylesheet" href="/static/css/jquery.fileupload-ui-noscript.css"></noscript>
    </head>
    <body>
    <!--最外层容器为.fileupload-buttonbar，内部包含-->
    <!--文件选择按钮 .fileinput-button (必须)，内部必须包裹一个input:file  multiple 批量上传-->
    <!--开始上传按钮 .start-->
    <!--取消上传按钮 .cancel-->
    <!--删除按钮 .delete-->
    <!--文件勾选按钮 .toggle-->
    <!--最外层容器为.fileupload-progress，内部包含-->
    <!--上传进度条容器.progress-->
    <!--上传进度条 .bar-->
    <!--上传进度文本 .progress-extended-->
    <!--显示容器-->
    <div class="container">
        <!-- 文件上传表单-->
        <!--可设置data-url 上传服务器地址-->
        <form id="fileupload" data-url="http://xxxx.com" method="POST" enctype="multipart/form-data">
            <!--包含添加 上传 开始 取消 和勾选按钮-->
            <div class="row fileupload-buttonbar">
                <div class="col-lg-7">
                    <span class="btn btn-success fileinput-button">
                        <i class="glyphicon glyphicon-plus"></i>
                        <span>Add Files...</span>
                        <input type="file" name="uploadfiles[]" multiple>
                    </span>
                    <button type="submit" class="btn btn-primary start">
                        <i class="glyphicon glyphicon-upload"></i>
                        <span>Start Upload</span>
                    </button>
                    <button type="reset" class="btn btn-warning cancel" >
                        <i class="glyphicon glyphicon-ban-circle"></i>
                        <span>Cancel upload</span>
                    </button>
                    <button type="button" class="btn btn-danger delete" >
                        <i class="glyphicon glyphicon-trash"></i>
                        <span>Delete</span>
                    </button>
                    <input type="checkbox" class="toggle" >
                    <!--文件处理状态-->
                    <span class="fileupload-process"></span>
                </div>
                <div class="col-lg-5 fileupload-progress fade">
                    <div class="progress progress-striped active" role="progressbar" aria-valuemin="0" aria-valuemax="100">
                        <div class="progress-bar progress-bar-success" style="width:0%;"></div>
                    </div>
                    <div class="progress-extended">&nbsp;</div>
                </div>
            </div>
            <table role="presentation" class="table table-striped"><tbody class="files"></tbody></table>
        </form>
    </div>
    <!-- 大图显示框 -->
    <div id="blueimp-gallery" class="blueimp-gallery blueimp-gallery-controls" data-filter=":even">
        <div class="slides"></div>
        <h3 class="title"></h3>
        <a class="prev">‹</a>
        <a class="next">›</a>
        <a class="close">×</a>
        <a class="play-pause"></a>
        <ol class="indicator"></ol>
    </div>
    
    <!--文件预览模板 (必须)-->
    <script id="template-upload" type="text/x-tmpl">
    {% for (var i=0, file; file=o.files[i]; i++) { %}
        <tr class="template-upload fade">
            <td>
                <span class="preview"></span>
            </td>
            <td>
                <p class="name">{%=file.name%}</p>
                <strong class="error text-danger"></strong>
            </td>
            <td>
                <p class="size">process...</p>
                <div class="progress progress-striped active" role="progressbar" aria-valuemin="0" aria-valuemax="100" aria-valuenow="0"><div class="progress-bar progress-bar-success" style="width:0%;"></div></div>
            </td>
            <td>
                {% if (!i && !o.options.autoUpload) { %}
                    <button class="btn btn-primary start" disabled>
                        <i class="glyphicon glyphicon-upload"></i>
                        <span>Start</span>
                    </button>
                {% } %}
                {% if (!i) { %}
                    <button class="btn btn-warning cancel">
                        <i class="glyphicon glyphicon-ban-circle"></i>
                        <span>Cancle</span>
                    </button>
                {% } %}
            </td>
        </tr>
    {% } %}
    </script>
    <!--文件上传完毕显示模板-->
    <script id="template-download" type="text/x-tmpl">
    {% for (var i=0, file; file=o.files[i]; i++) { %}
        <tr class="template-download fade">
            <td>
                <span class="preview">
                    {% if (file.thumbnailUrl) { %}
                        <a href="{%=file.url%}" title="{%=file.name%}" download="{%=file.name%}" data-gallery><img src="{%=file.thumbnailUrl%}"  height="80" width="80"></a>
                    {% } %}
                </span>
            </td>
            <td>
                <p class="name">
                    {% if (file.url) { %}
                        <a href="%=file.url%}" title="{%=file.name%}" download="{%=file.name%}" {%=file.thumbnailUrl?'data-gallery':''%}>{%=file.name%}</a>
                    {% } else { %}
                        <span>{%=file.name%}</span>
                    {% } %}
                </p>
                {% if (file.error) { %}
                    <div><span class="label label-danger">Error</span> {%=file.error%}</div>
                {% } %}
            </td>
            <td>
                <span class="size">{%=o.formatFileSize(file.size)%}</span>
            </td>
             {% if (!file.error) { %}
               <td>
                <p class="name">
                    <input type='text' value="{%=file.url%}"  size=70 />
                </p>
            </td>
                {% } %}
            <td>
                {% if (file.deleteUrl) { %}
                    <button class="btn btn-danger delete" data-type="{%=file.deleteType%}" data-url="{%=file.deleteUrl%}"{% if (file.deleteWithCredentials) { %} data-xhr-fields='{"withCredentials":true}'{% } %}>
                        <i class="glyphicon glyphicon-trash"></i>
                        <span>Delete</span>
                    </button>
                    <input type="checkbox" name="delete" value="1" class="toggle">
                {% } else { %}
                    <button class="btn btn-warning cancel">
                        <i class="glyphicon glyphicon-ban-circle"></i>
                        <span>Cancel</span>
                    </button>
                {% } %}
            </td>
        </tr>
    {% } %}d
    </script>
    
    </body>
    </html>
    
    
    <!--基础  jquery.js-->
    <script src="../js/jquery-3.1.1.js"></script>
    <!--ui显示插件-->
    <script src="../js/vendor/jquery.ui.widget.js"></script>
    <!--JS模板引擎 用于渲染上传、下载的项目-->
    <script src="../js/vendor/tmpl.min.js"></script>
    <!--Load Image 预览图片-->
    <script src="../js/vendor/load-image.all.min.js"></script>
    <!--图片剪裁需要用到Canvas to Blob plugin-->
    <script src="../js/vendor/canvas-to-blob.min.js"></script>
    <!--Bootstrap JS 用于响应式设计-->
    <script src="../js/vendor/bootstrap.min.js"></script>
    <!--图片carousel-->
    <script src="../js/vendor/jquery.blueimp-gallery.min.js"></script>
    <!--负责上传 -->
    <script src="../js/jquery.fileupload.js"></script>
    <!--上传处理  准备预览图-->
    <script src="../js/jquery.fileupload-process.js"></script>
    <!--待上传image 生成预览图 并剪裁   调用canvas.toBlob 绘制预览图-->
    <script src="../js/jquery.fileupload-image.js"></script>
    <!--文件检测  类型 大小  数量等-->
    <script src="../js/jquery.fileupload-validate.js"></script>
    <!--上传UI-->
    <script src="../js/jquery.fileupload-ui.js"></script>
    <!--设置上传参数 -->
    <script src="main.js"></script>
 
### 服务器返回数据格式
>这是标准的返回数据格式。这里可以定制化，返回的json数据用来渲染template-down模板，参数名对应即可，也可以添加自己需要的参数

    {
        "files": [
            {
                "name": "PNG.png",
                "size": 42971,
                "type": "image/png",
                "url": "http://localhost/UPLOAD/server/php/files/PNG.png",
                "thumbnailUrl": "http://localhost/UPLOAD/server/php/files/thumbnail/PNG.png",
                "deleteUrl": "http://localhost/UPLOAD/server/php/?file=PNG.png",
                "deleteType": "DELETE"
            }
        ]
    }
    
### Tips

> 跨域   
确保访问地址正常  
在服务器响应部分添加  
header("Access-Control-Allow-Origin: *"); # 跨域处理  
header("Access-Control-Allow-Methods:PUT,POST,GET,DELETE,OPTIONS");#允许的请求方法  


> 参数  
除了图片数据，可以用formData设置需要额外传递的参数  

    $('#fileupload').fileupload({
            dataType: 'json',
            multipart: true,//设置为true
            formData:[{name:'hello',value:'world'}]
        });


>服务端参考自带的server下服务端的实现代码
