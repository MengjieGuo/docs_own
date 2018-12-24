## django 实现在线编辑 doc 文档？

    
## 需求描述

    签发和签收公文，公文的文件格式为doc。该功能说的通俗一点就是多人协作版word。

    很明显与多人协作电子表格这一类的多人协作功能一样。

    同时需要考虑兼容 IE8+ 的浏览器，操作系统兼容 Windows XP+。

## 使用 ckeditor
    
<p class='tip'>
    ckeditor: 富文本编辑器<br><br>
    [cdeditor reference -- django doc](https://django-ckeditor.readthedocs.io/en/latest/)<br>
    [cdeditor reference -- github](https://github.com/django-ckeditor/django-ckeditor)
<p>

    django 集成了 ckeditor 

## 使用 django-docx-template 

    django-docx-template 模板模拟 word-office 操作界面
    
[django-docx-template](https://docxtpl.readthedocs.io/en/latest/)

