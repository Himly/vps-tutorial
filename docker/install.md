```sh

```

# 1 Docker & Docker Compose

- Debian / Ubuntu 更新

```sh
apt update -y  && apt upgrade -y && apt install -y curl wget sudo socat
```


## 1.1 安装/更新 官方版 docker

```sh
curl -fsSL https://get.docker.com | sh
```

- 查看 Docker 版本: `docker --version`

- 启动 Docker

```sh
systemctl enable docker
systemctl start docker

#查看docker服务状态
systemctl status docker
#查看docker容器信息
docker info
#查看docker容器帮助
docker --help
```


## 1.2安装/更新 官方版 docker-compose

```sh
curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

- 查看 Docker Compose 版本: `docker-compose --version`


# 2 镜像相关
- `docker rm`: 根据容器的名称或者 ID 来删除容器。
- `docker rmi`: 通过镜像的 ID 删除镜像

-  镜像检索: [https://hub.docker.com](https://hub.docker.com/search?q=)

- 查看本地镜像列表: `docker images` 或  `docker image ls`

- 搜索镜像: `docker search Nginx`

- 下载更新镜像: `docker pull nginx:1.21.0` (不带版本号则拉取最新版)

- 删除镜像: `docker rmi <your-image-id>`

- 一次删除所有镜像: `docker rmi $(docker images -q)` (-q 返回镜像的唯一id)

- 下载更新 compose 中所有镜像: `docker-compose pull`

- 删除 compose 中所有镜像: `docker-compose down --rmi all`


# 3 容器相关

- 查看已经创建的容器: `docker ps -a`

- 查看已经启动的容器: `docker ps -s`

- 启动容器名为con_name的容器: `docker start con_name`

- 停止容器名为con_name的容器: `docker stop con_name`

- 删除容器名为 con_name 的容器: `docker rm con_name` (先暂停再删除)

- 强制删除 nginx 容器: `docker rm -f nginx`

- 删除多个容器
```markdown
  1. 停止所有容器运行：docker stop $(docker ps -a -q)
  2. 删除所有停止运行的容器：docker rm $(docker ps -a -q)
```
     
- 启动所有容器: `docker start $(docker ps -a -q)`

- 暂停所有容器: `docker pause $(docker ps -a -q)`

- 开机自启动容器
  - 开启：`docker update --restart=always 容器名称`
  - 关闭：`docker update --restart=no 容器名称`

- 进入容器: `docker exec -it 容器名 bash`
