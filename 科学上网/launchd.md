## concept

launchd 是 macOS 下用于初始化系统环境的关键进程，类似 Linux 下的 init, rc。

[Daemons and Services Programming Guide](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html)

Many kinds of tasks that do not require user interaction are most effectively handled by a process that runs in the **background**.

Daemons and services are started by launchd in two separate session contexts:

![Startup_Session-and-Login_Session](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Art/bootstrap_session_2x.png)

Implement your Background Process

1. [Adding Login Items](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-BAJJBJEG)  
2. [Creating XPC Services](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6-SW1)  
3. [Creating Launch Daemons and Agents](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLaunchdJobs.html#//apple_ref/doc/uid/10000172i-SW7-BCIEDDBJ)  

	- Launch Daemon：在开机时载入（load）；  
	- Launch Agent：在用户登录时载入（load）；  

## manual

执行 `man launchd` 查看 launchd 说明文档。

```shell
faner@MBP-FAN:~|⇒  man launchd

launchd(8)                BSD System Manager's Manual               launchd(8)

NAME
     launchd -- System wide and per-user daemon/agent manager



FILES
     ~/Library/LaunchAgents         Per-user agents provided by the user.
     /Library/LaunchAgents          Per-user agents provided by the administrator.
     /Library/LaunchDaemons         System-wide daemons provided by the administrator.
     /System/Library/LaunchAgents   Per-user agents provided by Apple.
     /System/Library/LaunchDaemons  System-wide daemons provided by Apple.

SEE ALSO
     launchctl(1), launchd.plist(5),

DEVELOPER DOCUMENTATION
     The Daemons and Services Programming Guide can be found at the following URL:

```

执行 `launchctl help` 或 `man launchctl` 查看 launchctl 命令帮助。

```shell
faner@THOMASFAN-MB0:~|⇒  man launchctl 

launchctl(1)              BSD General Commands Manual             launchctl(1)

NAME
     launchctl -- Interfaces with launchd

SYNOPSIS
     launchctl subcommand [arguments ...]

DESCRIPTION
     launchctl interfaces with launchd to manage and inspect daemons, agents and XPC
     services.
```

### launchctl

```shell
faner@MBP-FAN:~|⇒  launchctl version
Darwin Bootstrapper Version 5.3.0: Thu May 31 18:56:58 PDT 2018; root:libxpc_executables-1205.70.9~7/launchd/RELEASE_X86_64
```

查看相关启动服务（list all of the jobs loaded into launchd）：

```shell
faner@MBP-FAN:~|⇒  launchctl list | egrep '(tencent|cisco)'
1285	0	com.tencent.QQMacMgrMonitor.3084
-	0	com.cisco.anyconnect.gui
-	0	com.apple.tencentweibo.xpc
-	0	com.tencent.QQMacMgrMonitor
```

### launchd.plist

Directory                       | Instruction
------------------------------- | ----------------------------------------------
`~/Library/LaunchAgents`        | Per-user agents provided by the user.
`/Library/LaunchAgents`         | Per-user agents provided by the administrator.
`/Library/LaunchDaemons`        | System-wide daemons provided by the administrator.
`/System/Library/LaunchAgents`  | Per-user agents provided by Apple.
`/System/Library/LaunchDaemons` | System-wide daemons provided by Apple.

执行 `man launchd.plist` 查看配置文件格式。

```shell
faner@THOMASFAN-MB0:~|⇒  man launchd.plist

launchd.plist(5)            BSD File Formats Manual           launchd.plist(5)

NAME
     launchd.plist -- System wide and per-user daemon/agent configuration files

DESCRIPTION
     This document details the parameters that can be given to an XML property list
     that can be loaded into launchd with launchctl.
```

brew 安装 cask 时自动安装依赖 emacs，并且设置加载 LaunchAgents 配置文件：

```Shell
~ $ brew install cask
==> Installing dependencies for cask: emacs
==> Installing cask dependency: emacs
==> Downloading https://homebrew.bintray.com/bottles/emacs-24.5.yosemite.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring emacs-24.5.yosemite.bottle.1.tar.gz
==> Caveats
To have launchd start emacs at login:
  ln -sfv /usr/local/opt/emacs/*.plist ~/Library/LaunchAgents
Then to load emacs now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.emacs.plist
==> Summary
🍺  /usr/local/Cellar/emacs/24.5: 3915 files, 105M
```

> [**launchd-keepalive**](https://github.com/tjluoma/launchd-keepalive)

## load & unload

### load

shadowsocks 的配置文件存储为 `~/Library/LaunchAgents/shadowsocks.plist`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <string>shadowsocks</string>
        <key>ProgramArguments</key>
        <array>
        <string>/Applications/ShadowsocksX.app/Contents/MacOS/ShadowsocksX</string>
        <string>-startup</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
    </dict>
</plist>
```

执行 `launchctl load` 命令加载 shadowsocks 配置：

```shell
launchctl load ~/Library/LaunchAgents/shadowsocks.plist
```

### unload

执行 `launchctl unload` 命令卸载 shadowsocks 配置：

```shell
launchctl unload ~/Library/LaunchAgents/shadowsocks.plist
```

以下通过 ls 和 egrep 命令查找 QQMacMgr 相关启动服务：

```shell
faner@MBP-FAN:~|⇒  ls -al ~/Library/LaunchAgents/ | egrep '(tencent|cisco)'

faner@MBP-FAN:~|⇒  ls -al /Library/LaunchAgents/ | egrep '(tencent|cisco)'
-rw-r--r--   1 root  wheel   635 Oct  7  2016 com.cisco.anyconnect.gui.plist
-rw-r--r--   1 root  admin   827 Oct 18  2015 com.tencent.QQMacMgrMonitor.plist

faner@MBP-FAN:~|⇒  ls -al /Library/LaunchDaemons | egrep '(tencent|cisco)'
-rw-r--r--   1 root  wheel   666 Oct  7  2016 com.cisco.anyconnect.vpnagentd.plist
-rw-r--r--@  1 root  admin   571 Oct  9  2015 com.tencent.QQMacMgr.plist
```

通过以下步骤可以取消 QQMacMgr 的自启动：

1. `sudo vim` 修改配置 plist 文件

	* KeepAlive 的值修改为 `<false/>`；  
	* RunAtLoad 的值修改为 `<false/>`。  

2. 执行 `launchctl unload  /Library/LaunchAgents/com.tencent.QQMacMgrMonitor.plist`  
3. 执行 `sudo killall -9 QQMacMgr`  

## references

[macOS 的 Launch Daemon / Agents](https://blog.csdn.net/left_la/article/details/40393057)  
[macOS 系统的 launchd 及守护进程 daemon](http://www.cnblogs.com/timelyxyz/p/3586136.html)  
[macOS启动服务优化高级篇（launchd tuning）](http://blog.csdn.net/guojin08/article/details/19925321)  

[三种方式配置 macOS 的启动项](https://blog.csdn.net/abby_sheen/article/details/7817198)  
[macOS 使用 launchctl 定时运行程序](https://blog.csdn.net/chuanzhilong/article/details/53466262)  
[macOS 下的运行管理器 —— Launchctl](https://blog.csdn.net/teamlet/article/details/50812615)  
[macOS 的定时任务利器 —— launchctl](https://www.jianshu.com/p/4addd9b455f2)  

[macOS 利用 launchctl 自启动 mySQL](http://squll369.iteye.com/blog/1965185)  
[macOS 使用 launchctl 设置开机启动 nginx](http://www.siguoya.name/pc/home/article/258)  
[macOS 利用 LaunchDaemons 开机启动 Tomcat 服务](https://blog.csdn.net/infoworld/article/details/47419877)  

[macOS 开机自动启动 shadowsocks](http://blog.yuzhuohui.info/2013/02/28/shadowsocks-on-mac-auto-start/) - node.js  
[macOS 中使用 launchd 自动启动 SS](http://gnailuy.com/mac/2015/05/19/launchd-configuration-for-shadowsocks/)  
[如何让 SHADOWSOCKS 在 macOS 上自动启动](http://www.wuliaole.com/post/how_to_setup_automatic_load_for_shadowsocks_on_mac/)  
