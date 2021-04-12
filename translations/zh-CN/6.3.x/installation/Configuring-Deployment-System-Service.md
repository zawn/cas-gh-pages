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

### Security

当以`root`执行时，即使用`root`用户去启动`init.d`服务时，CAS默认以拥有当前web程序文件的用户身份去运行web程序的可执行脚本。 您**永远** 不应该以 `root` 身份运行CAS，因此更不应该让`root`作为Web应用程序文件的拥有者。 而是应该创建一个特定用户以运行CAS，并使用 `chown` 使其成为文件的所有者。 例如：

```bash
chown bootapp:bootapp /path/to/cas.war
```

您还可以采取措施来防止修改CAS Web应用程序文件。 首先，将权限配置为使其无法写入，并且只能由其所有者读取或执行：

```bash
chmod 500 /path/to/cas.war
```

Additionally, you should also take steps to limit the damage if the CAS web application or the account that’s running it is compromised. If an attacker does gain access, they could make the web application file writable and change its contents. One way to protect against this is to make it immutable using `chattr`:

```bash
sudo chattr +i /path/to/cas.war
```

This will prevent any user, including `root`, from modifying the file.

## `systemd` 服务

To install CAS as a `systemd` service create a script named `cas.service` using the following example and place it in `/etc/systemd/system` directory:

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

<div class="alert alert-info"><strong>Not So Fast</strong><p>Remember to change the <code>Description</code>, <code>User</code> and <code>ExecStart</code> fields for your deployment.</p></div>

The user that runs the CAS web application, PID file and console log file are managed by `systemd` itself and therefore must be configured using appropriate fields in `service` script. Consult [the service unit configuration man page](https://www.freedesktop.org/software/systemd/man/systemd.service.html) for more details.

To flag the application to start automatically on system boot use the following command:

```bash
systemctl enable cas.service
```

Refer to `man systemctl` for more details.

## Upstart

[Upstart](http://upstart.ubuntu.com/) is an event-based service manager, a potential replacement for the System V init that offers more control on the behavior of the different daemons. When using Ubuntu you probably have it installed and configured already (check if there are any jobs with a name starting with `cas` in `/etc/init`).

We create a job `cas.conf` to start the CAS web application:

```bash
# Place in /home/{user}/.config/cas
description "CAS web application"
# attempt service restart if stops abruptly
respawn
exec java -jar /path/to/cas.war
```

Now run `start cas` and your service will start. Upstart offers many job configuration options and you can find [most of them here](http://upstart.ubuntu.com/cookbook/).

## Windows 服务

### Windows服务包装器

CAS may be started as Windows service using [winsw](https://github.com/kohsuke/winsw).

Winsw provides programmatic means to `install/uninstall/start/stop` a service. In addition, it may be used to run any kind of executable as a service under Windows.

Once you have downloaded the Winsw binaries, the `cas.xml` configuration file that defines our Windows service should look like this:

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

Finally, you have to rename the `winsw.exe` to `cas.exe` so that its name matches with the `cas.xml` configuration file. Thereafter you can install the service like so:

```bash
cas.exe install
```

Similarly, you may use `uninstall`, `start`, `stop`, etc.

Refer to [this example](https://github.com/snicoll-scratches/spring-boot-daemon) to learn more.

### 其他

CAS web applications may also be started as Windows service using [Procrun](http://commons.apache.org/proper/commons-daemon/procrun.html) of the [Apache Commons Daemon project](http://commons.apache.org/daemon/index.html). Procrun is a set of applications that allow Windows users to wrap Java applications as Windows services. Such a service may be set to start automatically when the machine boots and will continue to run without any user being logged on.
