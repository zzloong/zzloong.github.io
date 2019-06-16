---
title: 使用 Dockcer-Compose 安装 Gitlab 服务
date: 2019-06-15 13:38:59
categories:
tags:
keywords:
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g41w3v73rtj20m80ci3yj.jpg)

DevOpS 环节中，代码托管是必不可少的。本文介绍如何利用 docker-compose 搭建 Gitlab 服务。

<!-- more -->

## gitlab 镜像

gitlab 分为两个版本：

- `gitlab-ce` 社区版
- `gitlab-ee` 企业收费版

本文的镜像，使用的是社区版本。社区办的镜像，可以在[gitlab/gitlab-ce
](https://hub.docker.com/r/gitlab/gitlab-ce) 查看。

## 硬件要求

查看 [官宣-Requirements](https://docs.gitlab.com/ce/install/requirements.html#memory)

### CPU

- 1 core supports up to 100 users but the application can be a bit slower due to having all workers and background jobs running on the same core
- 2 cores is the recommended number of cores and supports up to 500 users
- 4 cores supports up to 2,000 users
- 8 cores supports up to 5,000 users
- 16 cores supports up to 10,000 users
- 32 cores supports up to 20,000 users
- 64 cores supports up to 40,000 users
- More users? Run it on [multiple application servers](https://about.gitlab.com/solutions/high-availability/)

### Memory

- 4GB RAM + 4GB swap supports up to 100 users but it will be very slow
- 8GB RAM is the recommended memory size for all installations and supports up to 100 users
- 16GB RAM supports up to 2,000 users
- 32GB RAM supports up to 4,000 users
- 64GB RAM supports up to 8,000 users
- 128GB RAM supports up to 16,000 users
- 256GB RAM supports up to 32,000 users
- More users? Run it on multiple application servers

## docker-compose 示例

```shell
version: "3"
services:
    gitlab:
        image: 'gitlab/gitlab-ce:latest'
        restart: always
        hostname: 'gitlab.michael.com'
        container_name: 'devops-gitlab'
        ports:
            - '81:80'
            - '443:443'
            - '23:22'
        environment:
            GITLAB_OMNIBUS_CONFIG: |
                external_url "http://gitlab.michael.com"
                gitlab_rails['gitlab_shell_ssh_port'] = 23
                gitlab_rails['time_zone'] = 'Asia/Shanghai'
                # email setting
                gitlab_rails['smtp_enable'] = true
                gitlab_rails['smtp_address'] = "smtp.163.com"
                gitlab_rails['smtp_port'] = 25
                gitlab_rails['smtp_user_name'] = "michael_show728@163.com"
                gitlab_rails['smtp_password'] = "xxx"
                gitlab_rails['smtp_domain'] = "163.com"
                gitlab_rails['smtp_authentication'] = "login"
                gitlab_rails['smtp_enable_starttls_auto'] = true
                gitlab_rails['gitlab_email_from'] = "michael_show728@163.com"
                gitlab_rails['smtp_tls'] = false
                gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
                user["git_user_email"] = "michael_show728@163.com"
        volumes:
            - '/data/gitlab/config:/etc/gitlab'
            - '/data/gitlab/logs:/var/log/gitlab'
            - '/data/gitlab/data:/var/opt/gitlab'
        networks:
            - gitlab_net

networks:
    gitlab_net:
        name: gitlab_net
```

为了在我机器上 `192.168.3.66` 能够识别这个域名，我通过配置 `hosts` 文件，将容器的宿主机 IP 和这个域名做了映射：

```shell
192.168.3.43 gitlab.michael.com
```

{% note info no-icon %}
经过试验，挂卷的目录，即宿主机的目录，如果没有创建的话，默认会创建的。有时候没有配置文件是没关系的，但是有时候却会有影响，比如 Nginx 的配置文件你没有事先归档在宿主机的目录上，这时候启动 Nginx 就不会起作用。

这里启动 gitlab-ce 没有问题，所以，无须利用 docker cp 的方式，预先拷贝一些配置文件出来。
{% endnote %}

```shell
$ docker exec -it devops-gitlab bash
root@gitlab:/# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.240.2   gitlab.michael.com gitlab
```

可以看到，在容器网络中，gitlab.michael.com 这个域名对应的 IP 是 `192.168.240.2`。我在局域网里用我自己另一台机器 ping 这个 IP，是不通的。因为我在家是没有 DNS 域名服务器的。这个 IP 很关键，在后面的配置中起到了关键作用。

## 配置详解

### ports

端口 `81:80` 的配置是因为我容器的宿主机的 80 端口是给 Nginx 用了。在同一台机器上，为了不引起端口冲突，将 81 端口和容器的 80 端口映射。 访问 gitlab 服务下面地址皆可：

- `宿主机 IP:81`，即 `192.168.3.43:81`
- `gitlab.michael.com:81`

这样的网址并不优美，因此，我在 Nginx 中增加了一个 `gitlab.conf` 的配置文件，这样就可以直接使用 `gitlab.michael.com` 访问了：

```shell
upstream  gitlab{
    # 域名对应 gitlab配置中的 external_url
    # 端口对应 gitlab 配置中的 nginx['listen_port']，通过环境变量可设置
    server 192.168.3.43:81;
}


server{
    listen 80;
    # 此域名是提供给最终用户的访问地址
    server_name gitlab.michael.com;

    location / {
        # 这个大小的设置非常重要，如果 git 版本库里面有大文件，设置的太小，文件push 会失败，根据情况调整
        client_max_body_size 50m;

        proxy_redirect off;
        #以下确保 gitlab中项目的 url 是域名而不是 http://git，不可缺少
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # 反向代理到 gitlab 内置的 nginx
        proxy_pass http://gitlab;
        index index.html index.htm;
    }
}
```

关于使用容器搭建 Nginx 可参考之前的文章：[使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)

端口 `'23:22'` 表示将宿主机的端口 23 和容器的端口 22 映射。ssh 默认端口就是 22，我们使用 ssh 协议登录宿主机时，就是用的 22 端口。因此，如果 `22:22` 这种映射的话，会影响登录宿主机的。也有文章修改了宿主机的 sshd 默认端口，但是我没有这么做。有需要的话，可以查看[通过 docker 搭建自用的 gitlab 服务](https://juejin.im/post/5a4c9ff36fb9a04507700fcc)

`gitlab_rails['gitlab_shell_ssh_port'] = 23` 这个环境变量的设置很关键，当我们选择 ssh 协议方式克隆代码库时，复制的地址将会自动变为：`ssh://git@gitlab.michael.com:23/root/gitlab-demo.git`

```shell
# michael @ Michael-MBP in ~/Code/00-Temp [15:25:21]
$ git clone ssh://git@gitlab.michael.com:23/root/gitlab-demo.git
Cloning into 'gitlab-demo'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (6/6), done.
```

### external_url

这里的 `external_url` 配置的是外部 URL，会影响项目的访问地址，如果不配置，项目的访问地址会是一个随机字符串，在云服务器上搭建时尤其要注意这一点。

例如，新建项目的 HTTP 地址为：`http://gitlab.michael.com/root/gitlab-demo`，这里的地址中的 `gitlab.michael.com` 就是我在 `docker-compose.yml` 文件中配置的环境变量。

{% note success no-icon %}
我发现仅配置了 `hostname` 为域名时，外部 URL 默认和这个域名保持了一致，也实现了 `external_url` 的作用。
{% endnote %}

### 开启邮件服务

我们在使用 github 等类似的平台工具的时候都会用到邮件服务，比如你在 github 上进行注册、密码重置、有人给你的开源项目提 issue 等等的时候，你通常都会收到邮件提醒。 gitlab 肯定也会有这个功能的，下面我们就来开启这个功能，在此之前需要准备一个邮箱账号，这个账号是用来负责发送邮件的，需要开启 smtp 协议支持。本文以 163 邮箱为例。

说明：

- `gitlab_rails['smtp_address']` ：SMTP服务地址，不同的服务商不同
- `gitlab_rails['smtp_port']` ：服务端口
- `gitlab_rails['smtp_user_name']` ：用户名，自己注册的
- `gitlab_rails['smtp_password']` ：客户端授权秘钥（获取方式，下图讲解）
- `gitlab_rails['gitlab_email_from']` ：发出邮件的用户，注意跟用户名保持一致
- `user["git_user_email"]` ：发出用户，注意跟用户名保持一致

亲测，当你使用管理员账户登录时，添加用户之后，上面的配置能够成功发送邮件：

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g41uqkxowbj20ze0b775y.jpg)

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g41w79zw81j20p10ddq4x.jpg)

了解更多，查看 [官网 SMTP 设置](https://docs.gitlab.com/omnibus/settings/smtp.html))

### 数据持久化

通过挂卷的方式，将 gitlab 的相关数据保存到宿主机上：

| 当地的位置          | 容器的位置      | 作用                   |
| ------------------- | --------------- | ---------------------- |
| /data/gitlab/config | /etc/gitlab     | 用于存储GitLab配置文件 |
| /data/gitlab/logs   | /var/log/gitlab | 用于存储日志           |
| /data/gitlab/data   | /var/opt/gitlab | 用于存储应用数据       |

## gitlab-runner

dockerhub 中有[gitlab/gitlab-runner](https://hub.docker.com/r/gitlab/gitlab-runner/tags)的景象。

Runner 的配置文件是 `/etc/gitlab-runner/config.toml`，它的字段说明，可以查看文档：

- [Gitlab-runner Advanced configuration](https://gitlab.com/gitlab-org/gitlab-runner/blob/master/docs/configuration/advanced-configuration.md)

### 创建 runner 容器

先这种方式运行一下，将配置文件拷贝出来，之后再通过 docker-compose 的方式启动：

```shell
mkdir -p /data/gitlab-runner
docker run -d --name gitlab-runner --restart always \
   -v /var/run/docker.sock:/var/run/docker.sock \
   --network=gitlab_net \
   --add-host=gitlab.michael.com:192.168.240.2 \
   gitlab/gitlab-runner:latest
docker cp gitlab-runner:/etc/gitlab-runner /data/gitlab-runner/config
```

上面这种方式，通过 `network` 的方式，将 runner 和 gitlab server 机器处于同一网络下。所以，当在 runner 中配置 host 之后，IP 就是可达的了。域名会被解析到。

```shell
$ docker exec -it gitlab-runner bash
root@55fc04a0d51a:/# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.240.2    gitlab.michael.com
192.168.240.3   55fc04a0d51a
```

将之前的 runner 容器停止：

```shell
docker stop gitlab-runner
docker rm gitlab-runner
```

docker-compose.yml 示例：

```shell
version: "2.1"
services:
  runner:
    image: 'gitlab/gitlab-runner:latest'
    container_name: 'gitlab-runner'
    restart: always
    volumes:
      - '/data/gitlab-runner/config:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'
    networks:
      - gitlab_net
    extra_hosts:
      - "gitlab.michael.com:192.168.240.2"

networks:
  gitlab_net:
    external: true
```

### 获取 Runner 信息

gitlab-runner 容器正常运行之后，注册到 gitlab 上的方式有两处：

- 管理员从 管理中心-Runner 中，注册时使用这里的 `shared runner token`，这种方式添加的 Runner 将作为 `shared Runner`
- 进入具体的项目 设置-CI/CD-Runner-专用 Runner，这种方式添加的 Runner 将作为 `专用 Runner`

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g41yzy7i10j20z50e741d.jpg)

### 注册 Runner

注册 Runner 时，[关于 excutor](https://docs.gitlab.com/runner/executors/README.html) 有好几种类型可以选择，我尝试了 shell 和 docker 两种类型。

#### shell

```shell
$ docker exec -it gitlab-runner gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=32 revision=ac2a293c version=11.11.2
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://gitlab.michael.com/
Please enter the gitlab-ci token for this runner:
Wt3GuGq1nAdKWaeBrGM7
Please enter the gitlab-ci description for this runner:
[8bc594b5f4f4]: shared runner
Please enter the gitlab-ci tags for this runner (comma separated):
centos,shared
Registering runner... succeeded                     runner=P6Pme8J5
Please enter the executor: ssh, docker+machine, kubernetes, parallels, docker-ssh, shell, virtualbox, docker-ssh+machine, docker:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```

#### docker

添加共享 runner：

```shell
$ docker exec -it gitlab-runner gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=31 revision=ac2a293c version=11.11.2
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://gitlab.michael.com/
Please enter the gitlab-ci token for this runner:
5SSZ_QcjnLFv9s36ALsL
Please enter the gitlab-ci description for this runner:
[7e5c74d87239]:
Please enter the gitlab-ci tags for this runner (comma separated):
docker
Registering runner... succeeded                     runner=5SSZ_Qcj
Please enter the executor: ssh, virtualbox, docker+machine, docker-ssh+machine, kubernetes, docker-ssh, parallels, shell, docker:
docker
Please enter the default Docker image (e.g. ruby:2.1):
alpine:latest
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```

选择 docker 为 excutor 时，config.toml 内容配置成这样，实现了网络的畅通：

```shell
$ cat config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "michael demo"
  url = "http://gitlab.michael.com/"
  token = "Wt3GuGq1nAdKWaeBrGM7"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    extra_hosts = ["gitlab.michael.com:192.168.240.2"]
    network_mode = "gitlab_net"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
```

主要就是添加了网络配置：

```shell
    extra_hosts = ["gitlab.michael.com:192.168.240.2"]
    network_mode = "gitlab_net"
```

> gitlab-runner 虽然是基于 docker 安装的，但是它每个 stage 执行是由 runner 在主机另起一个 docker 容器来执行的，执行完毕后自动销毁，并不是在 gitlab-runner 容器内部启动新的容器。 runner 相当于一个在主机的 agent 程序，来负责接收任务、分发并创建新的容器执行任务，最后回传执行结果到 gitlab。

### 设置 runner

这时候，我们可以在「管理中心」的 Runner 中看到所有的 Runner 信息。

{% note warning no-icon %}
注意点：在同一个 git-runner 容器中，注册过之后，重置了 token，上一个 token 就会失效。尽管在管理中心会看到两个 runner，但是通过查看 git-runner 日志，其中有一个是失效的。

此外，我一开始注册之后，流水线始终提示缺少 runner。很奇怪，因为去 Runner 界面查看，是 `available` 状态的。后来去管理中心看了一下，原来，新创建的runner，默认情况下，只对打 tag 的 commit 触发任务，否则会找不到对应 tag 的机器！除非，你勾选上无需 tag。
{% endnote %}

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g4221cdnkqj20yw0iaacy.jpg)

此外，同一个 gitlab-runner 我分别注册了两次，设置的 tag 不一样。使用的是项目的 token 和 共享的 token，二者皆是有效的。这时候注册的时候，会显示两个 runner，二者都会被成功调用。

## CI/CD

关于 gitlab 的 CI/CD 功能，更多可以查看 [GitLab Documentation GitLab CI/CD GitLab CI/CD Pipeline Configuration Reference](https://docs.gitlab.com/ee/ci/yaml/README.html)。

### 流水线

要使用流水线功能，前提是 repo 中要有 `.gitlab-ci.yml` 文件。

一个示例：

```shell
stages:
  - install_deps
  - test
  - build
  - deploy_test
  - deploy_production

# 安装依赖
install_deps:
  stage: install_deps
  only:
    - develop
    - master
  script:
    - pwd
    - echo "now install_deps stage"
  tags:
  - docker

# 运行测试用例
test:
  stage: test
  only:
    - develop
    - master
  script:
    - pwd
    - echo "now test stage"
  tags:
  - shell

# 编译
build:
  stage: build
  only:
    - develop
    - master
  script:
    - pwd
    - echo "now build stage 1"
  tags:
  - docker

# 部署测试服务器
deploy_test:
  stage: deploy_test
  only:
    - develop
  script:
    - echo "now deploy_test stage"

# 部署生产服务器
deploy_production:
  stage: deploy_production
  only:
    - master
  script:
    - echo "now deploy_production stage"
```

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g421vw7in7j21jp0ekwgk.jpg)

## 参考

gitlab-ce

- [Omnibus GitLab documentation](https://docs.gitlab.com/omnibus/README.html)
- [Omnibus GitLab documentation Configuring omnibus settings Configuration options](https://docs.gitlab.com/omnibus/settings/configuration.html)
- [cnodejs-使用docker-compose搭建gitlab](https://cnodejs.org/topic/5bb4caa69545eaf107b9c7e6) 挺全的，还介绍了 gitlab runner 的使用，推荐
- [掘金-通过 docker 搭建自用的 gitlab 服务](https://juejin.im/post/5a4c9ff36fb9a04507700fcc)
- [segmentfault-使用docker搭建gitlab环境](https://segmentfault.com/a/1190000014305359) 邮箱配置参考

gitlab-runner

- [Run GitLab Runner in a container](https://docs.gitlab.com/runner/install/docker.html)
- [千峰教育-基于 Docker 安装 GitLab Runner](https://www.funtl.com/zh/apache-dubbo-ci/%E5%9F%BA%E4%BA%8E-Docker-%E5%AE%89%E8%A3%85-GitLab-Runner.html)
- [Building Docker images with GitLab CI/CD](https://gitlab.com/help/ci/docker/using_docker_build.md) 利用 Gitlab CI/CD 构建 Docker 镜像，可以查看
- [Docker搭建自己的Gitlab CI Runner](https://blog.csdn.net/aixiaoyang168/article/details/72168834)

CI/CD

- [GitLab Documentation GitLab CI/CD GitLab CI/CD Pipeline Configuration Reference](https://docs.gitlab.com/ee/ci/yaml/README.html)
