

## api文档管理工具

[origin refernce](https://www.cnblogs.com/libaoli/p/8295835.html)

### 1、国外的话Swagger
swagger-ui

### 2、国内的Showdoc
国内开源的非常好用的一款API文档管理系统，安装也非常方便，只需将源代码放到项目目录下自动安装运行即可，不要要注意PHP版本必须大于5.3.



### 3、界面简洁功能强大的EOAPI

### 4、阿里的RAP
Web API management, free and open sourced, mock data generator, auto test, made by Alibaba, using by 1000+ corporations! Web接口管理工具，开源免费，接口自动化，MOCK数据自动生成，自动化测试，企业级管理。阿里妈妈MUX团队出品！阿里巴巴都在用！1000+公司的选择！一直被抄袭，从未被超越 :3 http://rapapi.net

### 5、postMan

### 6、docute: 无需编译的文档撰写工具
像 gitbook 之类的工具需要编译后发布，而 docute 让你直接写 markdown 文件作为文档来显示而不需要编译成 html 这一步，你的文档目录里只需要一个首页 index.html 和你的配置文件 config.js。

docute 会直接渲染这些 markdown 文件为一个单页应用。配合 github pages 发布到 ./docs 目录效果更佳，当然发布到任何地方都可以。

docute 的文档: https://docute.js.org/#/zh-Hans/ (基于 Vue)

Github 项目：https://github.com/egoist/docute

### 7、SmartWiki 接口文档在线管理系统

### 8、SosoApi
SosoApi,编辑Swagger UI的神器

### 9、CrapApi开源接口管理系统演示地址

CrapApi：一个由angularjs+bootstrap+springMVC搭建的高性能的免费开源的API接口、文档管理系统（应用接口管理系统）

GitHub源码地址：https://github.com/EhsanTang/...
码云源码地址：http://git.oschina.net/CrapAp...

主要功能：api接口管理、数据字典管理、接口数据模拟、接口文档管理（支持markdown、kindereditor等编辑器）、支持本地部署或在线使用、支持通过建表语句导入数据字典...

其他功能：项目管理、用户管理、模块管理、接口管理、接口版本管理、接口拷贝、接口文档pdf下载、接口mock、模块加密访问、
接口在线调试、数据字典管理、数据字典加密访问多管理员、多权限、多角色管理、自定义菜单、自定义网站样式、文档留言、
错误码管理、接口排序、DOC、PDF、TEXT、EXCEL等资源管理、支持版本号控制、文档内容检索、操作日志记录、
根据日志恢复数据、markdown编辑器、kindeditor编辑器、angularjs编辑器、Lucene搜索、项目成员管理...

站点使用地址：http://api2.crap.cn
效果图：

###  10、 TeaKKi
这个WIKI用来写文档也不错~
TeaKKi == team wiki, https://teakki.com 超越了wiki, 在wiki基础上支持团队知识协作

### 11、Web API文档生成工具apidoc

这个需要在编辑后生成静态的HTML页面，然后上传到服务器。

工具名称：apiDoc 
Git地址：https://github.com/apidoc/apidoc 
项目地址：http://apidocjs.com/ 
样例项目：http://apidocjs.com/example_b... 
apoDoc是从源码的注释中生成RestFul api 文档，样子还是蛮漂亮的……

使用apidoc 生成Restful web Api文档
NodeJS、NPM安装配置步骤(windows版本)

Apidoc安装与使用

具体步骤：
Windows安装方法：
1、官网nodejs.org下载Node

2、安装

3、将npm 替换为淘宝镜像cnpm

C:\Users\Administrator>npm install -g cnpm --registry=https://registry.npm.taobao.org
4、使用cnpm安装apidoc

C:\Users\Administrator>cnpm install apidoc -g
安装的地址：

[apidoc@0.17.5] link C:\Users\Administrator\AppData\Roaming\npm\apidoc@ -> C:\Users\Administrator\AppData\Roaming\npm\node_modules\apidoc\bin\apidoc

C:\Users\Administrator\AppData\Roaming\npm\node_modules\apidoc
apidoc 监视文件变动自动生成工具
监控api_doc.json文件改变，然后自动生成HTML

watch.js文件，然后node 执行该文件

/**
 * watch.js
 * Created by lincoln on 16-1-6.
 */
var gaze = require('gaze');
var exec = require('child_process').exec;
var fs = require('fs')


function init(){
    fs.mkdirSync('./api');
    fs.mkdirSync('./doc');
    createConfigureFile();
    beginWatch();
}
/**
{
  "name": "测试",
  "version": "0.0.1",
  "description": "API文档测试",
  "title": "API文档测试",
  "url" : "http://121.41.44.218",
  "sampleUrl" : "http://121.41.44.218",
  "template":{
    "forceLanguage":"zh-cn"
  }
}
*/

function createConfigureFile(){
    var configure = {
      "name": "测试",
      "version": "0.0.1",
      "description": "API文档测试",
      "title": "API文档测试",
      "url" : "http://xxxxxx",
      "sampleUrl" : "http://xxx",
      "template":{
        "forceLanguage":"zh-cn"
      }
    }
    fs.writeFileSync('./api/apidoc.json',JSON.stringify(configure));
}

function beginWatch(){
    gaze('./api/*.*',function(error,watcher){
     this.on('all', function(event, filepath) {
        console.log(filepath + ' was ' + event);
        runGeneartion();
      })
    });
}

function runGeneartion(){
    var com = exec('apidoc -i ./api -o ./doc ')
    com.stdout.on('data', function (data) {
        console.log("生成Api->"+data);
    });

    com.stderr.on('data', function (data) {
        console.log('生成错误啦->' + data);
    });
}

if(fs.existsSync('./api') && fs.existsSync('./doc')){
    beginWatch();
}else{
    init();
}
安装gaze包，然后执行watch.js文件：

➜  apidoc cnpm install gaze
[gaze@*] installed at node_modules/.1.1.2@gaze (14 packages, use 2s, speed 54.93kB/s, json 85.75kB, tarball 0B)
All packages installed (14 packages installed from npm registry, use 2s, speed 54.62kB/s, json 17(85.75kB), tarball 0B)
➜  apidoc node watch.js
/WEB/apidoc/api/apidoc.json was changed
生成Api->info: Done.
api参数：

Usage: C:\Program Files\nodejs\node.exe apidoc [options]

Options:
   -f, --file-filters      RegEx-Filter to select files that should be parsed (multiple -f can be used)
   -e, --exclude-filters   RegEx-Filter to select files / dirs that should not be parsed (many -e can b
   -i, --input             Input / source dirname.  [./]
   -o, --output            Output dirname.  [./doc/]
   -t, --template          Use template for output files.  [C:\Users\Administrator\AppData\Roaming\npm\
   -c, --config            Path to directory containing config file (apidoc.json)  [./]
   -p, --private           Include private APIs in output.  [false]
   -v, --verbose           Verbose debug output.  [false]
   -h, --help              Show this help information.
   --debug                 Show debug messages.  [false]
   --color                 Turn off log color.  [true]
   --parse                 Parse only the files and return the data, no file creation.  [false]
   --parse-filters         Optional user defined filters. Format name=filename
   --parse-languages       Optional user defined languages. Format name=filename
   --parse-parsers         Optional user defined parsers. Format name=filename
   --parse-workers         Optional user defined workers. Format name=filename
   --silent                Turn all output off.  [false]
   --simulate              Execute but not write any file.  [false]
   --markdown              Turn off default markdown parser or set a file to a custom parser.  [true]
   --line-ending           Turn off autodetect line-ending. Allowed values: LF, CR, CRLF.
   --encoding              Set the encoding of the source code. [utf8].  [utf8]
执行生成文档命令
D:\code\api_doc>apidoc -i example/ -o doc/
info: Done.

D:\POCO\api_doc>
12、PHP_DOC 实时生成 API 文档
https://segmentfault.com/a/11...

