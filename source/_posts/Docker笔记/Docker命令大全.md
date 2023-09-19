---
title: Docker命令大全
date: 2022-11-04 13:48:06
id: 32
tags: Docker
categories: Docker笔记
copyright: true
---

## 1. Docker 引擎命令

### 1.1 Docker version 命令

**docker version** : 显示 Docker 版本信息。

```shell
Usage:
  docker version [flags]

Flags:
  -f, --format string       格式化输出. Values: [pretty | json]. (Default: pretty)
```

### 1.2 Docker info 命令

**docker info** : 显示 Docker 系统信息，包括镜像和容器数。

‍

### 1.3 Docker help 命令

**docker help** : 显示指定命令的帮助

```shell
Usage:  docker help [command]
```

### 1.4 Docker events 命令

**docker events** : 从服务器获取实时事件

```shell
Usage:  docker events [OPTIONS]

Options:
  -f, --filter filter   根据条件过滤事件
      --since string    从指定的时间戳后显示所有事件
      --until string    流水时间显示到指定的时间为止
```

## 2. Docker 仓库命令

### 2.1 Docker search 命令

**d**​**ocker search** : 在 Docker Hub 上搜索镜像

```shell
Usage:  docker search [OPTIONS] TERM

Options:
  -f, --filter filter   通过搜索来过滤
      --limit int       搜索结果的最大数量 (默认 25)
      --no-trunc        不截断输出

--filter=STARS=3000     # 搜索出来的镜像就是stars大于3000的
```

### 2.2 Docker login 命令

**docker login** : 登录镜像服务器

```shell
Usage:
  docker login [OPTIONS] [SERVER] [flags]
  docker login [command]

Flags:
  -p, --password string   密码
      --password-stdin    从标准输入获取密码
  -u, --username string   用户名
```

### 2.3 Docker logout 命令

**docker logout** : 退出镜像服务器

```shell
Usage:
  docker logout [SERVER] [flags]
  docker logout [command]
```

### 2.4 Docker pull 命令

**docker pull** : 下载镜像

```shell
Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Options:
  -a, --all-tags                下载存储库中所有带标签的镜像
      --disable-content-trust   跳过镜像验证(默认为true)
      --platform string         如果服务器是多平台的，则设置平台
  -q, --quiet                   抑制详细输出
```

### 2.5 Docker push 命令

**docker push** : 上传镜像或者容器到远程镜像服务器

```shell
Usage:  docker push [OPTIONS] NAME[:TAG]

Push an image or a repository to a registry

Options:
  -a, --all-tags                上传存储库中所有带标签的镜像
      --disable-content-trust   跳过镜像签名(默认为true)
  -q, --quiet                   抑制详细输出
```

## 3. Docker 镜像命令

### 3.1 Docker images 命令

**docker images** : 查看所有本地主机上的镜像

```shell
Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             显示所有镜像
      --digests         显示sha256摘要
  -f, --filter filter   根据提供的条件筛选输出
      --no-trunc        不截断输出
  -q, --quiet           仅显示镜像id
```

### 3.2 Docker rmi 命令

**docker rmi** : 删除一个或者多个镜像

```shell
Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Options:
  -f, --force      强制删除镜像
      --no-prune   不移除该镜像的过程镜像，默认移除

docker rmi -f 镜像id                 # 删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id    # 删除多个镜像（空格分隔）
docker rmi -f $(docker images -aq)  # 删除全部的镜像
```

### 3.3 Docker tag 命令

**docker tag** : 标记本地镜像，将其归入某一仓库

```shell
Usage:  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

### 3.4 Docker history 命令

**docker history** : 查看指定镜像的创建历史。

```shell
Usage:  docker history [OPTIONS] IMAGE

Options:
  -H, --human           以可读的格式打印镜像大小和日期，默认为true
      --no-trunc        显示完整的提交记录
  -q, --quiet           仅列出提交记录ID
```

### 3.5 Docker build 命令

**docker build** : 使用 Dockerfile 创建镜像。

```shell
Usage:  docker build [OPTIONS] PATH | URL | -

Options:
      --add-host list           添加自定义的主机IP映射 (host:ip)
      --build-arg list          设置镜像创建时的变量
      --cache-from strings      要考虑作为缓存源的镜像
      --disable-content-trust   忽略校验，默认开启
  -f, --file string             指定要使用的Dockerfile路径
      --iidfile string          将镜像ID写入文件
      --isolation string        使用容器隔离技术
      --label list              设置镜像使用的元数据
      --network string          默认 default。在构建期间设置RUN指令的网络模式
      --no-cache                创建镜像的过程不使用缓存
  -o, --output stringArray      输出目的地(format:type=local,dest=path)
      --platform string         如果服务器是多平台的，则设置平台
      --progress string         设置进度输出类型 (auto, plain,
                                tty). Use plain to show container output
                                (default "auto")
      --pull                    尝试去更新镜像的新版本
  -q, --quiet                   安静模式，成功后只输出镜像 ID
  -t, --tag list                镜像的名字及标签，通常 name:tag 或者 name 格式
      --target string           Set the target build stage to build.
```

## 4. Docker 容器生命周期命令

### 4.1 Docker run 命令

**docker run** : 创建一个新的容器并运行一个命令

```shell
Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Options:
      --add-host list                  Add a custom host-to-IP mapping
                                       (host:ip)
  -a, --attach list                    指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项
      --blkio-weight uint16            Block IO (relative weight),
                                       between 10 and 1000, or 0 to
                                       disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device
                                       weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the
                                       container
      --cgroupns string                Cgroup namespace to use
                                       (host|private)
                                       'host':    Run the container in
                                       the Docker host's cgroup namespace
                                       'private': Run the container in
                                       its own private cgroup namespace
                                       '':        Use the cgroup
                                       namespace as configured by the
                                                  default-cgroupns-mode
                                       option on the daemon (default)
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair
                                       Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair
                                       Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in
                                       microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in
                                       microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution
                                       (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution
                                       (0-3, 0,1)
  -d, --detach                         后台运行容器，并返回容器ID
      --detach-keys string             Override the key sequence for
                                       detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed
                                       devices list
      --device-read-bps list           Limit read rate (bytes per second)
                                       from a device (default [])
      --device-read-iops list          Limit read rate (IO per second)
                                       from a device (default [])
      --device-write-bps list          Limit write rate (bytes per
                                       second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second)
                                       to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       指定容器使用的DNS服务器，默认和宿主一致
      --dns-option list                Set DNS options
      --dns-search list                指定容器DNS搜索域名，默认和宿主一致
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT
                                       of the image
  -e, --env list                       设置环境变量
      --env-file list                  从指定文件读入环境变量
      --expose list                    开放一个端口或一组端口
      --gpus gpu-request               GPU devices to add to the
                                       container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check
                                       (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to
                                       report unhealthy
      --health-start-period duration   Start period for the container to
                                       initialize before starting
                                       health-retries countdown
                                       (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to
                                       run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                指定容器的hostname
      --init                           Run an init inside the container
                                       that forwards signals and reaps
                                       processes
  -i, --interactive                    以交互模式运行容器，通常与 -t 同时使用
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      添加链接到另一个容器
      --link-local-ip list             Container IPv4/IPv6 link-local
                                       addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g.,
                                       92:d0:c6:0a:29:33)
  -m, --memory bytes                   设置容器使用内存最大值
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus
                                       swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness
                                       (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the
                                       container
      --name string                    容器名字：用来区分容器
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the
                                       container
      --no-healthcheck                 Disable any container-specified
                                       HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000
                                       to 1000)
      --pid string                     设置容器的 PID 模式
      --pids-limit int                 Tune container pids limit (set -1
                                       for unlimited)
      --platform string                Set platform if server is
                                       multi-platform capable
      --privileged                     默认情况下container是不能访问任何其他设备的。
				       但是通过"privileged"，container就拥有了访问任何其他设备的权限
  -p, --publish list                   指定端口映射，格式为：主机(宿主)端口:容器端口
  -P, --publish-all                    随机端口映射，容器内部端口随机映射到主机的端口
      --pull string                    Pull image before running
                                       ("always"|"missing"|"never")
                                       (default "missing")
      --read-only                      启用后，容器的文件系统将为只读
      --restart string                 在容器退出时重新启动策略应用 (default "no")
					no，默认策略，在容器退出时不重启容器
					on-failure，在容器非正常退出时（退出状态非 0），才会重启容器
					on-failure:3，在容器非正常退出时重启容器，最多重启 3 次
					always，在容器退出时总是重启容器，当操作系统或 docker 服务重启时，该容器总能随系统启动
					unless-stopped，在容器退出时总是重启容器，但是不考虑在 Docker 守护进程启动时就已经停止了的容器
      --rm                             当容器退出时如果容器关闭，则自动清除所有该容器的信息
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the
                                       process (default true)
      --stop-signal string             Signal to stop a container
                                       (default "15")
      --stop-timeout int               Timeout (in seconds) to stop a
                                       container
      --storage-opt list               Storage driver options for the
                                       container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            为容器重新分配一个伪输入终端，通常与 -i 同时使用
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format:
                                       <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    绑定一个卷
      --volume-driver string           容器的可选卷驱动程序
      --volumes-from list              从其他容器挂载目录
  -w, --workdir string                 设置容器的工作目录
```

‍

### 4.2 Docker create 命令

**docker create** : 创建一个新的容器但不启动它，语法同 docker run

```shell
Usage:  docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

### 4.3 Docker start 命令

### 4.4 Docker stop 命令

### 4.5 Docker restart 命令

### 4.6 Docker pause 命令

### 4.7 Docker unpause 命令

### 4.8 Docker kill 命令

### 4.9 Docker rm 命令
