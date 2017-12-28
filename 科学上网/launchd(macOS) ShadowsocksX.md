## launchd
[Mac系统的launchd、守护进程daemon](http://www.cnblogs.com/timelyxyz/p/3586136.html)  
[Mac OS启动服务优化高级篇（launchd tuning）](http://blog.csdn.net/guojin08/article/details/19925321)  

[Mac开机自动启动shadowsocks](http://blog.yuzhuohui.info/2013/02/28/shadowsocks-on-mac-auto-start/) - node.js  
[macOS 中使用 launchd 自动启动 SS](http://gnailuy.com/mac/2015/05/19/launchd-configuration-for-shadowsocks/)  
[如何让SHADOWSOCKS在MAC上自动启动](http://www.wuliaole.com/post/how_to_setup_automatic_load_for_shadowsocks_on_mac/)  

launchd 是 macOS 下用于初始化系统环境的关键进程，类似 Linux 下的 init, rc。

```shell
faner@THOMASFAN-MB0:~|⇒  launchctl version
Darwin Bootstrapper Version 5.2.0: Tue Oct 31 15:18:53 PDT 2017; root:libxpc_executables-1205.30.29~14/launchd/RELEASE_X86_64
```

### manual

执行 `man launchd` 查看 launchd 说明文档。

```shell
faner@THOMASFAN-MB0:~|⇒  man launchd

launchd(8)                BSD System Manager's Manual               launchd(8)

NAME
     launchd -- System wide and per-user daemon/agent manager
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

配置文件存储为 `~/Library/LaunchAgents/shadowsocks.plist`：

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

### load

```shell
launchctl load ~/Library/LaunchAgents/shadowsocks.plist
```

### unload

```shell
launchctl unload ~/Library/LaunchAgents/shadowsocks.plist
```