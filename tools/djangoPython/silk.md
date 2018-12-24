## INSTALLED_APP

```python
    ...
    'silk',
```

## MIDDLEWARE

```python
    'silk.middleware.SilkyMiddleware',
    ...
```

## other

```python
    # Silk profiling configuration.
    # Turn on the SILKY_PYTHON_PROFILER setting to use Python's built-in cProfile profiler.
    SILKY_PYTHON_PROFILER = True
    # If you would like to also generate a binary .prof file set the following:
    # SILKY_PYTHON_PROFILER_BINARY = True
    # SILKY_STORAGE_CLASS = 'silk.storage.ProfilerResultStorage'
    # If this is not set, MEDIA_ROOT will be used.
    # SILKY_PYTHON_PROFILER_RESULT_PATH = '/Users/screwman/Downloads/SilkUsage/profiles/'
    SILKY_AUTHENTICATION = True
    SILKY_AUTHORISATION = True
    SILKY_MAX_REQUEST_BODY_SIZE = - 1  # Silk取任何<0为无限制
    SILKY_MAX_RESPONSE_BODY_SIZE = 1024  # 如果响应体> 1024kb，则忽略
    SILKY_META = True  # 有时候能够看到Silk对请求/响应时间的影响是有用的
    # SILKY_INTERCEPT_PERCENT = 50  # 仅记录50％的请求
    # SILKY_MAX_RECORDED_REQUESTS = 10 ** 4  # 可以设置config var以限制它存储的请求/响应行的数量。
    # SILKY_MAX_RECORDED_REQUESTS_CHECK_PERCENT = 10  # 垃圾收集仅在一定比例的请求上运行以减少开销。可以使用此配置进行调整：
```

## profile view method

```python
    from silk.profiling.profiler import silk_profile

    class GatewayDo(View):
        @silk_profile(name='Get gateway')
        def post(self, request):
            resp = {'code': '8999', 'data_type': '2', 'response_data': '', 'msg': ''}
```

## profile normal method

```python
    @silk_profile(name='Get vehicles of user')
    def get_vehicle_detail(self) ->dict:
```

## profile query

```python
    with silk_profile(name='Get vehicle from db #'):
        vehicles = get_vehicles(self.user)
```

## url

```python
urlpatterns += [url(r'^silk/', include('silk.urls', namespace='silk'))]
```

## migrate

```python
    python manage.py makemigrations silk

    python manage.py migrate silk

    python manage.py collectstatic
```

<p class='tip'>
<strong>If you want to access silk, You must login from xadmin</strong>
<p>