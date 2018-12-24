# django-vue项目如何添加访问后端的url前缀

<p class='tip'>
    这里的url包括请求静态文件的url和api的url
<p>

## 1. nginx配置

首先需要配置的是nginx，在实际项目中，是通过203服务器的nginx通过location规则转发到198的服务器上。

因为203服务器上的location规则是 /alipay/ 。为了适配该规则，同时能让nginx维护static目录，因此需要在198服务器项目中的nginx做以下配置：
    .. image:: ../_static/static-01.png
若配置成功（必须配置成功），则效果是这样的：
    .. image:: ../_static/static-02.png


## 2. setting文件配置

nginx配置成功后，接下来需要配置django项目的setting文件中关于static文件的相关配置，具体配置如下::

```python
    STATIC_URL = '/alipay/static/'  # 同样，这是为了适配nginx的规则。
    STATIC_ROOT = os.path.join(BASE_DIR, "static") # static文件的存放目录。
```

在经过这样的配置后，django将会正则匹配 /alipay/static/ 这个url，去 static 目录下查找静态文件。与上面的nginx刚好吻合。

## 3. vue静态文件路径设置

举例来说，若使用最初的设置，进行npm run build，则生成的index.html文件中，引用静态文件的路径如下::

```javascript
    <script type=text/javascript src=/static/js/manifest.02b173356cbf4a22f3f3.js></script>
```
很明显，这样的url是无法和上面setting文件配置的STATIC_URL进行正则匹配，就会造成404错误。

此时，我们需要修改vue的build设置，方法如下：
    #. 找到 frontend/config/index.js 文件。
    #. 做以下修改::
```js
        build: {
            env: require('./prod.env'),
            index: path.resolve(__dirname, '../dist/index.html'),
            assetsRoot: path.resolve(__dirname, '../dist'),
            assetsSubDirectory: 'alipay/static',  # 只需要做这一处修改即可，原设置是：assetsSubDirectory: 'static',
            assetsPublicPath: '/',
            productionSourceMap: true,
            ...
          },
```
修改之后，生成的index.html文件中，引用静态文件的路径就改变了::
```js
    <script type=text/javascript src=/alipay/static/js/manifest.02b173356cbf4a22f3f3.js></script>
```
完美适配setting文件配置的STATIC_URL路径。

但是，需要注意的是，与此同时，生成的项目结构也发生了变化，如下图所示：
    .. image:: ../_static/static-03.png

不过不需要惊慌，理解到位的话，只需要做一些相应的变化即可。

## 4. setting文件配置
-------------------------------------------------------------------------------
又回到了setting文件配置，此时需要配置的是，static资源目录。只需要将上面的目录结构抄下来即可。如下所示::
```python
    STATICFILES_DIRS = [  # 静态资源目录，该配置规定了需要被收集的静态资源位置
        os.path.join(BASE_DIR, 'frontend/dist/alipay/static'),
    ]
```
做了这个配置后，使用 python manage.py collectstatic 命令，就会正常收集vue的静态文件到static目录下。

