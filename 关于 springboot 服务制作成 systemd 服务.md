### 关于 springboot 服务制作成 systemd 服务

> 今天写了一个监控程序，用来监控区妇幼的免费孕检号（周末的号真的太难抢了，而且每次放号的时间点不确定），写完之后就是 `install jar` ，下面就是所有 java web 开发人员熟知的步骤，丢到服务器，写一个启动脚本（又或者直接 `nohup java -jar XX.jar 2>&1 &` 启动，不想每次都敲命令，写个简单的脚本）。但是，懒得自己写，去抄袭脚本的过程中，发现了另一种更加简便的方式，那就是制作成 systemd 服务，照着操作了一遍，欢喜雀跃，这里记录下，顺便推荐让大家也雀跃一下

##### 1、那么首先 systemd 服务，是个什么啥？

    systemd 是 Linux 上的一种系统和服务管理器，用于管理系统上运行的进程和服务。systemd 服务是一种可以由 systemd 管理的后台进程或服务。通过将 Spring Boot 应用程序制作为 systemd 服务，我们可以使用 systemd 的各种功能来启动、停止、重启和管理应用程序。

##### 2、如何制作 systemd 服务

首先，我们需要创建一个名为 `my-spring-boot-app.service` 的 systemd 服务文件。在 Linux 中，通常将此文件存储在 `/etc/systemd/system/` 目录下。



my-spring-boot-app.service 内容为：

```shell
[Unit]
Description=My Spring Boot Application  #名称描述
After=syslog.target #告诉 systemd 在哪个主要系统服务后启动本服务

[Service]
User=myuser #应用程序运行的用户
WorkingDirectory=/path/to/my/springboot/app #应用程序的工作目录
Group=mygroup #组信息
ExecStart=/usr/bin/java -Xms256m -Xmx512m -jar /path/to/my/springboot/app/my-spring-boot-app.jar --server.port=8080
SuccessExitStatus=143 #应用程序退出时的退出状态码，设置为 143 表示systemctl stop 命令下服务可以快速关闭
TimeoutStopSec=10 #表示在关闭服务前等待的时间
Restart=always #设置服务崩溃时自动重启服务
RestartSec=5 #在崩溃后五秒重试
LimitNOFILE=65536 #表示服务最多可以打开 65536 个文件
ExecStop=/usr/bin/pkill -f my-spring-boot-app.jar #执行 systemctl 时执行的命令
#ExecStop=/bin/kill -15 $MAINPID

[Install]
WantedBy=multi-user.target #服务会在 multi-user.target 启动后启动
```

##### 3、重新加载 systemd 配置

添加服务文件后，需要重新加载 systemd 配置以更新系统。使用以下命令重新加载 systemd 配置：

```shell
sudo systemctl daemon-reload
```

##### 4、启动 systemd 服务

```shell
sudo systemctl start my-spring-boot-app
```

##### 5、查看服务状态

```shell
sudo systemctl status my-spring-boot-app
```

6、停止服务

```shell
sudo systemctl stop my-spring-boot-app
```

7、设置服务自启动和禁止自启动

```shell
sudo systemctl enable my-spring-boot-app   #设置服务自启动
sudo systemctl disable my-spring-boot-app  #取消服务自启动
```



#### 总结一下：

通过将 Spring Boot 应用程序制作为 systemd 服务，我们可以使用 systemd 的各种功能来管理应用程序。这包括自动启动应用程序、监视应用程序、自动重启应用程序以及将应用程序与其他系统服务集成。

制作 systemd 服务的过程并不复杂，只需要创建一个服务文件并将应用程序配置到其中即可。通过 systemd，我们可以让应用程序更加稳定、可靠地运行在系统上。
