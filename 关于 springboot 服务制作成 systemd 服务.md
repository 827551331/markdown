### 关于 springboot 服务制作成 systemd 服务

> 今天写了一个监控程序，用来监控区妇幼的免费孕检号（周末的号真的太难抢了，而且每次放号的时间点不确定），写完之后就是` install jar` ，下面就是所有 java web 开发人员熟知的步骤，丢到服务器，写一个启动脚本（又或者直接 `nohup java -jar XX.jar 2>&1 &` 启动，不想每次都敲命令，写个简单的脚本）。但是，懒得自己写，去抄袭脚本的过程中，发现了另一种更加简便的方式，那就是制作成 systemd 服务，照着操作了一遍，欢喜雀跃，这里记录下，顺便推荐让大家也雀跃一下

##### 1、那么首先 systemd 服务，是个什么东西？

        systemd 是 Linux 上的一种系统和服务管理器，用于管理系统上运行的进程和服务。systemd 服务是一种可以由 systemd 管理的后台进程或服务。通过将 Spring Boot 应用程序制作为 systemd 服务，我们可以使用 systemd 的各种功能来启动、停止、重启和管理应用程序。



##### 2、如何制作 systemd 服务？

###### 2.1、创建 systemd 服务文件

首先，我们需要创建一个名为 `my-spring-boot-app.service` 的 systemd 服务文件。在 Linux 中，通常将此文件存储在 `/etc/systemd/system/` 目录下。使用以下命令创建文件：
