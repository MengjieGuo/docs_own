## 准备

<p class="warning">
  <strong>确保安装 docker 服务 及 docker-compose</strong><br><br>
</p>


1. 安装 docker-ce 社区版本 or docker-ee 专业版，富裕的同学可以安装 docker-ee。
2. 安装 docker-compose

## 制作

## 测试

## 上传到远程仓库

<p class='tip'>
这里假设使用阿里的镜像仓库服务，当然如果使用dockerhub或者自建仓库都是ok的。<br>
如果是自建仓库，docker也有镜像可以启动仓库服务。
</p>

```bash
#!/bin/sh
# 推送镜像到远程仓库上面
your_user_name='SuperMan'
your_project_name="HelloWorld"
version_number='1.1.1'
# May be, you should rename the image name to adjust remote Repository.
# docker tag local_image_name:version registry.cn-hangzhou.aliyuncs.com/{your_user_name}/{your_project_name}:{version_number}
docker push registry.cn-hangzhou.aliyuncs.com/{your_user_name}/{your_project_name}:{version_number}

```
## 从远程仓库下载

<p class='tip'>
这里假设使用阿里的镜像仓库服务，当然如果使用 dockerhub 或者 自建仓库 都是ok的。<br>
如果是自建仓库，docker 也有镜像可以启动仓库服务。
</p>

```bash
#!/bin/sh
# 拉取远程仓库上面的镜像
your_user_name='SuperMan'
your_project_name="HelloWorld"
version_number='1.1.1'
docker pull registry.cn-hangzhou.aliyuncs.com/{your_user_name}/{your_project_name}:{version_number}

```
