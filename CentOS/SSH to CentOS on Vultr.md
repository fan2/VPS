## add SSH keys
在 Servers 页，点击 SSH Keys 链接，参考 [How Do I Generate SSH Keys?](https://www.vultr.com/docs/how-do-i-generate-ssh-keys/) 教程

![SSH_Key-Add](images/SSH_Key-Add.png)

![SSH_Key-Added](images/SSH_Key-Added.png)

```shell
faner@THOMASFAN-MB0:~|⇒  ssh -i ~/.ssh/id_rsa root@ip.ip.ip.ip
```

> -i identity_file

```shell
faner@THOMASFAN-MB0:~|⇒  ssh root@ip.ip.ip.ip
The authenticity of host 'ip.ip.ip.ip (ip.ip.ip.ip)' can't be established.
ECDSA key fingerprint is SHA256:*******************************************.
Are you sure you want to continue connecting (yes/no)? 
```

默认会提示一个 SSH 密钥，输入 `yes` 同意，提示输入 root 账户密码：

```shell
Warning: Permanently added 'ip.ip.ip.ip' (ECDSA) to the list of known hosts.
root@ip.ip.ip.ip's password: 
```

输入正确的 root 账户密码后，成功登录进 VPS 服务器（hostname 为 vfan）：

```shell
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[root@vfan ~]# 
```

## reinstall

```shell
faner@THOMASFAN-MB0:~|⇒  ssh root@ip.ip.ip.ip
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:*******************************************.
Please contact your system administrator.
Add correct host key in /Users/faner/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/faner/.ssh/known_hosts:16
ECDSA host key for ip.ip.ip.ip has changed and you have requested strict checking.
Host key verification failed.
faner@THOMASFAN-MB0:~|⇒  
```

**解决方法**：移除 Vultr 服务器 `ip.ip.ip.ip` 对应的行，然后重试。

## change password
在 SSH 中输入 `sudo passwd root` 回车

```shell
sudo passwd root
```

系统会提示输入两遍 root 密码，输入想设的密码即可。

## 光标无响应
```shell
ssh -i ~/.ssh/id_rsa -o ServerAliveInterval=60 root@ip.ip.ip.ip
```

[解决ssh连接一段时间后光标无响应问题](http://blog.csdn.net/u010521546/article/details/45623469)  
