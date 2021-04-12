---
layout: default
title: CAS - 部署系统服务
category: 安装
---

# CAS - 部署系统服务

CAS 可以很容易地使用 `init.d` 或 `systemd` 作为Unix/Linux 服务启动。 要支持Windows也可以通过外部守护程序进行设置。 请注意，[在此说明一下](Configuring-Servlet-Container.html#embedded)，以下这些配置大多数是针对使用嵌入式容器运行CAS的。

## `init.d` 服务

如果CAS被构建并并作为 [一个完全可执行的web应用程序](Configuring-Servlet-Container.html)运行为，那么它可以被用作 `init.d` 服务。 简单地使用 `符号链接` 将Web应用程序文件链接到 `init.d`以支持标准的 `start`, `stop`, `restart` and `status` 的命令。

CAS中内置的配置允许它与OS系统配置进行交互，如下所示：

- 以拥有jar文件的用户身份启动服务
- 使用 `/var/run/cas/cas.pid` 追踪CAS web 应用程序的 PID
- 将控制台日志写入 `/var/log/cas.log`

要将CAS作为 `init.d` 服务安装，只需创建一个符号链接：

```bash
sudo ln -s /path/to/cas.war /etc/init.d/cas
service cas start
```

您还可以标记应用程序，以便使用您的操作系统提供的工具来配置自动启动。 例如，在Debian上：

```bash
update-rc.d myapp defaults <priority>
```

### 安全性

当以`root`执行时，即使用`root`用户去启动`init.d`服务时，CAS默认以拥有当前web程序文件的用户身份去运行web程序的可执行脚本。 您**永远** 不应该以 `root` 身份运行CAS，因此更不应该让`root`作为Web应用程序文件的拥有者。 而是应该创建一个特定用户以运行CAS，并使用 `chown` 使其成为文件的所有者。 例如：

```bash
chown bootapp:bootapp /path/to/cas.war
```

您还可以采取措施来防止修改CAS Web应用程序文件。 首先，将权限配置为使其无法写入，并且只能由其所有者读取或执行：

```bash
chmod 500 /path/to/cas.war
```

此外，你也应该采取措施降低损失，防止CAS Web应用程序或正在运行它的帐户被泄露。 如果攻击者确实获得了访问权限，则他们可以使Web应用程序文件可写并更改其内容。 防止这种情况的一个办法是使用 `chattr` 使它变得不可变：

```bash
sudo chattr +i /path/to/cas.war
```

这将防止任何用户修改文件，包括 `root`。

## `systemd` 服务

要将CAS作为 `systemd` 服务安装，请使用以下示例创建一个名为 `cas.service` 的脚本，并将其放在 `/etc/systemd/system`目录中：

```ini
[Unit]
Description=CAS
After=syslog.target

[Service]
User=bootapp
ExecStart=/path/to/cas.war
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

<div class="alert alert-info"><strong>別著急</strong><p>请根据你的部署情况对<code>Description</code>, <code>User</code> 和 <code>ExecStart</code>进行设置。</p></div>

通过这种方式运行CAS Web应用程序用户PID文件和控制台日志文件由 `systemd` 本身管理，因此必须在 `service` 脚本中相应的字段进行合适的配置。 有关更多详细信息，请查阅[服务单元配置手册](https://www.freedesktop.org/software/systemd/man/systemd.service.html)

要将应用程序标记为在系统引导时自动启动，请使用以下命令：

```bash
systemctl enable cas.service
```

有关更多详细信息，请参考 `man systemctl`

## Upstart

[Upstart](http://upstart.ubuntu.com/) 是一个基于事件的服务管理器，它是System V init的潜在替代品，它提供了对不同守护程序行为的更多控制。 当使用Ubuntu时，您可能已经安装并配置了它（检查 `/etc/init`中是否有任何名称以 `cas` 开头的作业）。

我们创建一个作业 `cas.conf` 以启动CAS Web应用程序：

```bash
# Place in /home/{user}/.config/cas
description "CAS web application"
# attempt service restart if stops abruptly
respawn
exec java -jar /path/to/cas.war
```

现在运行 `start cas` 命令 ，您的服务将启动。 Upstart 提供了许多作业配置选项，并且您可以在这里找到 [大多数选项。](http://upstart.ubuntu.com/cookbook/)。

## Windows 服务

### Windows服务包装器

可以使用[winsw](https://github.com/kohsuke/winsw)将CAS作为Windows服务启动。

Winsw提供了编程方式来 `install/uninstall/start/stop`服务。 另外，它可以在Windows下将任何类型的可执行文件作为服务运行。

下载Winsw二进制文件后，使用`cas.xml` 文件定义我们的Windows服务，如下所示：

```xml
<service>
    <id>cas</id>
    <name>CAS</name>
    <description>CAS web application.</description>
    <executable>java</executable>
    <arguments>-Xmx2048m -jar "path\to\cas.war"</arguments>
    <logmode>rotate</logmode>
</service>
```

最后，必须将 `winsw.exe` 重命名为 `cas.exe` 以便其名称与 `cas.xml` 配置文件匹配。 之后，您可以像这样安装服务：

```bash
cas.exe install
```

类似地，可以使用 `uninstall`, `start`, `stop`等命令进行其他操作。

请参考[本示例](https://github.com/snicoll-scratches/spring-boot-daemon)以了解更多信息。

### 其他

也可以使用 [Apache Commons Daemon 项目](http://commons.apache.org/daemon/index.html)的 [Proculn](http://commons.apache.org/proper/commons-daemon/procrun.html) 将CAS web 应用程序作为Windows服务启动。 Procrun是一组应用程序，允许Windows用户将Java应用程序包装为Windows服务。 可以将此类服务设置为在计算机启动时自动启动，并且将在没有任何用户登录的情况下继续运行。
