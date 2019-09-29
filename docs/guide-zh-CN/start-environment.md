## 环境搭建

目录

- 安装 docker
- 安装 docker-compose
- 镜像加速
- 初始化环境

### 安装 docker

> 以下以 CentOS 为例  
> 更多详见官方文档：https://docs.docker.com/install/linux/docker-ce/centos/

1、卸载老的 docker 版本(没有可忽略)

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2、 安装所需要的包

```
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

3、安装 docker

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

4、启动 docker

```
sudo systemctl start docker
```

### 安装 docker-compose

> 以下以 Linux 为例  
> 更多详见官方文档：https://docs.docker.com/compose/install/

1、安装

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2、赋予权限

```
sudo chmod +x /usr/local/bin/docker-compose
```

3、查看版本号

```
docker-compose --version
```

### 镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。国内很多云服务商都提供了国内加速器服务，例如：

- [Azure 中国镜像 https://dockerhub.azk8s.cn](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)
- [阿里云加速器(需登录账号获取)](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
- [七牛云加速器 https://reg-mirror.qiniu.com](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)

> 由于镜像服务可能出现宕机，建议同时配置多个镜像。

> 国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务，具体请参考官方文档。

本节我们以 Azure 中国镜像 https://dockerhub.azk8s.cn 为例进行介绍。

**Ubuntu 16.04+、Debian 8+、CentOS 7**

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```

> 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。

之后重新启动服务。

```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

> 注意：如果您之前查看旧教程，修改了 docker.service 文件内容，请去掉您添加的内容（--registry-mirror=https://dockerhub.azk8s.cn）。

#### Windows 10

对于使用 Windows 10 的用户，在任务栏托盘 Docker 图标内右键菜单选择 Settings，打开配置窗口后在左侧导航菜单选择 Docker Engine，在右侧像下边一样编辑 json 文件，之后点击 Apply & Restart 保存后 Docker 就会重启并应用配置的镜像地址了。

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```

#### macOS

对于使用 macOS 的用户，在任务栏点击 Docker Desktop 应用图标 -> Perferences，在左侧导航菜单选择 Docker Engine，在右侧像下边一样编辑 json 文件。修改完成之后，点击 Apply & Restart 按钮，Docker 就会重启并应用配置的镜像地址了。

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```

#### 检查加速器是否生效

执行 `$ docker info`，如果从结果中看到了如下内容，说明配置成功。

```
Registry Mirrors:
 https://dockerhub.azk8s.cn/
```

#### gcr.io 镜像

国内无法直接获取 gcr.io/* 镜像，我们可以将 gcr.io/<repo-name>/<image-name>:<version> 替换为 gcr.azk8s.cn/<repo-name>/<image-name>:<version> ,例如

```
# $ docker pull gcr.io/google_containers/hyperkube-amd64:v1.9.2

$ docker pull gcr.azk8s.cn/google_containers/hyperkube-amd64:v1.9.2
```

源文章 [docker使用国内镜像](https://github.com/yeasy/docker_practice/blob/master/install/mirror.md)

### 初始化环境

1、直接clone项目

```
git clone https://github.com/jianyan74/dockerfiles.git
```

2、进入目录

```
cd dockerfiles
```
3、生成 elasticsearch 插件目录 (不需要可跳过)

```
mkdir data/elasticsearch/plugins
```

4、授权日志目录

```
chmod -R 777 logs
```

5、授权持久化数据目录

```
chmod -R 777 data
```

正式安装看[程序安装](start-install.md)