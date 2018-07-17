## devOps 环境搭建文档

### 1. git 服务器(gitLab)

>gitLab 的优点：
>1. 账号系统及 web 私钥管理
>2. 友好的 web 代码查看、issue、milestone、在线 code review 等
>3. git 工作流拓展：hook、CI/CD 等
>4. 跟github一样，不需要额外的学习成本

#### **1.1 硬件需求：**

1. cpu 2核4线程
2. 内存8G
3. 硬盘200G

#### **1.2 环境要求:**

1. linux 64-bit 系统 kernel 3.10+
2. git version 1.8 + (参数附录1)
3. docker-ce 17.03+(参见附录2)
4. docker-compose 1.11.0 + (参见附录3)

#### **1.3 安装文档**
>说明：
>  传统方式是手动下载Gitlab的软件包，然后搭建相关运行环境。不过这种方式非常麻烦，而且如果要更换机器所配置工作又得重来一边;人生苦短,所以本文档使用 docker 安装gitLab，docker-compose管理gitLab容器

##### 1.3.1 安装基础环境(参数环境要求)
&nbsp;&nbsp;&nbsp;&nbsp;完成环境需求里的依赖安装，docker并免sudo(见附录4)

##### 1.3.2 编写一个 docker-compose.yml 文件，并运行

```shell
  #切换到home目录
  cd ~
  
  vi docker-compose.yml
  #####复制以下信息#####
  version: '2'
    services:
    gitlab:
    image: 'gitlab/gitlab-ce:latest'
    container_name: gitlab
    restart: always
    hostname: 'gitlab.example.com'
    environment:
        TZ: 'Asia/Shanghai'
        GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
        # Add any other gitlab.rb configuration here, each on its own line
    ports:
        - '80:80'
        #用于开启gitlab-pages功能
        - '8080:8080'
        - '3022:22'
    volumes:
        - '~/gitlab/config:/etc/gitlab'
        - '~/gitlab/logs:/var/log/gitlab'
        - '~/gitlab/data:/var/opt/gitlab'
    #####end#####
    #在当前目录执行
    docker-compose up -d
    #注意初次执行大约等待5min左右，可以用下述方法查看gitlab是否初始化完成Ctrl- C退出
    docker-compose logs -f
```
##### 1.3.3 访问gitLab(默认是设置root用户的密码)，并设置root用户的密码
![gitLab-init](http://git.airocov.com/breeder/working-notes/raw/master/assets/web_server/gitLab-init.png)

##### 1.3.4 邮箱配置

- 修改配置文件 : ~/gitlab/gitlab.rb

```shell
    ##找到对应的地方，并修改成下面的样子(邮箱地址、密码与smtp server根据实际情况填写)
    ### Email Settings
    gitlab_rails['gitlab_email_enabled'] = true
    gitlab_rails['gitlab_email_from'] = 'example@example.com'
    gitlab_rails['gitlab_email_display_name'] = 'Example'
    gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'
    #gitlab_rails['gitlab_email_subject_suffix'] = ''

    ### GitLab email server settings
    ###! Docs: https://docs.gitlab.com/omnibus/settings/smtp.html
    ###! **Use smtp instead of sendmail/postfix.**

    gitlab_rails['smtp_enable'] = true
    gitlab_rails['smtp_address'] = "smtp.server"
    gitlab_rails['smtp_port'] = 465
    gitlab_rails['smtp_user_name'] = "smtp user"
    gitlab_rails['smtp_password'] = "smtp password"
    gitlab_rails['smtp_domain'] = "example.com"
    gitlab_rails['smtp_authentication'] = "login"
    gitlab_rails['smtp_enable_starttls_auto'] = true
    gitlab_rails['smtp_tls'] = true

    ###! **Can be: 'none', 'peer', 'client_once', 'fail_if_no_peer_cert'**
    ###! Docs: http://api.rubyonrails.org/classes/ActionMailer/Base.html
    gitlab_rails['smtp_openssl_verify_mode'] = 'none'
```

- 测试是否配置成功

```shell
    #进入容器
    docker exec -it gitlab bash
    #或 
    docker-compose exec gitlab bash
    #重新加载配置文件
    gitlab-ctl reconfigure  
    #测试发送邮件
    gitlab-rails console 
    >Notify.test_email('729679720@qq.com', 'Message Subject', 'Message Body').deliver_now #测试发送邮件
```

到此 gitLab 搭建完成

### 2. gitLab-runner服务器

> Gitlab-CI是Gitlab官方提供的持续集成服务，在仓库的根目录下新建.gitlab-ci.yml文件，并且在gitlab中配置runner，在之后的每次提交合并中将会触发构建,实现编译、测试、打包和发布。

#### 2.1 硬件需求：

1. cpu 4核 4线程
2. 内存 8G
3. 硬盘 200G

#### 2.2 环境要求:

1. linux 64-bit 系统 kernel 3.10+
2. docker-ce 17.03+ (参见附录2)

#### 2.3 git-runner 与gitLab-ci的关系图

![gitLab-ci](http://git.airocov.com/breeder/working-notes/raw/master/assets/web_server/gitLab-ci.png)

#### 2.4 安装文档

##### 2.4.1 安装基础环境(参见环境要求)

&nbsp;&nbsp;&nbsp;&nbsp;完成环境需求里的依赖安装，docker并免sudo(见附录4)

##### 2.4.2 docker 安装 gitLab-runner
 
 ```shell
    docker run -d --name gitlab-runner --restart always \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v /srv/gitlab-runner/config:/etc/gitlab-runner \
            gitlab/gitlab-runner:latest
 ```

##### 2.4.3 在 gitLab CI server 中 注册 gitLab-runner

>只有通过认证的Runner才可以连接到Gitlab CI Server上，所以需要一个注册过程

```shell
    docker exec -it gitlab-runner gitlab-runner register
```

注册文档详见 [Registering Runners](https://docs.gitlab.com/runner/register/),注册完成后登录root账户查看runner:  Admin Area -> Overview -> Runners 查看注册完成的runner。

##### 2.4.4 测试gitlab-runner能否正常运行

- 在gitlab上新建一个仓库

- 在仓库根目录新建一个.gitlab-ci.yml文件，文件内容如下：
>gitlab-runner的 tags是用来标识.gitlab-ci.yml是用哪个runner解析
```yml
    image: alpine:latest
    stages:
    - test
    - build
    - deploy

    test:
    stage: test
    script: echo "Running tests"
    tags:
    - 你注册runner时填写的tag 
    build:
    stage: build
    script: echo "Building the app"
    tags:
    - 你注册runner时填写的tag 

    deploy:
    stage: deploy
    script:
        - echo "Deploy to staging server"
    only:
        - master  
    tags:
        - 你注册runner时填写的tag 
```
- push gitlab-ci.yml到仓库，然后到 CI/CD -> Pipelines 去看具体的结果

到此gitLab-runner 安装完成

##### 2.4.5 在安装过程中遇到的bug汇总

1. gitlab-runner 安装 注册完成 , 编辑 .gitlab-yml 上传并触发持续集成，但是failed,查看日志后报错：
    `exec: "xz": executable file not found in $PATH`
    解决方法：

    在宿主机上安装xz-utils, gitlab [issue地址](https://gitlab.com/gitlab-org/gitlab-runner/issues/2533)

```shell
    sudo apt-get install xz-utils
```



###3. 附录
<font color="red">注意：</font> 本文档如果出现国内安装方式与国外安装方式，是指国内访问比较慢，但是可以使用。
1. 安装git 

- 在 Debian / Ubuntu 上安装

```shell
    # 更新源 (可以选择更改国内源来提高速度，这里就不详细说明了)
    sudo apt-get -y update
    # 包管理器安装
    sudo apt-get -y install git
```

- 在 Red Hat / CentOS 上安装

```shell
    #更新源 (可以选择更改国内源来提高速度，这里就不详细说明了)
    sudo yum -y update 
    # 包管理器安装
    sudo yum -y install git 
```

2. 安装docker

- linux 上安装 docker 

```shell
    # 国外安装方式
    curl -sSL https://get.docker.com | sh
    # 国内安装方式
    curl -sSL https://get.daocloud.io/docker | sh
```

3. 安装docker-compose

docker-compose[版本列表](https://github.com/docker/compose/releases)

```shell
    #切换到root用户
    sudo su
    #国外安装方式
    curl -L https://github.com/docker/compose/releases/download/{指定版本}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    #国内安装方式
    curl -L https://get.daocloud.io/docker/compose/releases/download/{指定版本}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
    #退出root用户
    exit
```

4. 免sudo 使用docker

使用上述方法安装docker之后，必须使用sudo docker，为了使用方便，所以让docker 免sudo执行

```shell
    #将用户加入该 group 内。
    sudo gpasswd -a ${USER} docker
    #重启docker服务器
    sudo service docker restart

    newgrp - docker
    #或者
    pkill X
```
5. gitlab-pages 开启

参见[本地搭建的GitLab中开启Pages功能，不需要域名也可以](http://www.daxiblog.com/2018/05/17/%E6%9C%AC%E5%9C%B0%E6%90%AD%E5%BB%BA%E7%9A%84gitlab%E4%B8%AD%E5%BC%80%E5%90%AFpages%E5%8A%9F%E8%83%BD%EF%BC%8C%E4%B8%8D%E9%9C%80%E8%A6%81%E5%9F%9F%E5%90%8D%E4%B9%9F%E5%8F%AF%E4%BB%A5/),但是有一点需要注意的是 

gitlab-ctl restart 重启GitLab，GitLab Pages功能不生效,需用重新加载配置的方法(docker 安装gitlab,版本为10.8.4 (2268d0c)

 ```shell
    #重新加载配置文件, 必须重新加载配置文件，不然nginx没有 gitlab-pages.conf文件
    gitlab-ctl reconfigure  
 ```

 6. GitLab代码自动备份

 参见[docker部署的GitLab代码自动备份](https://blog.csdn.net/u014258541/article/details/79317180)