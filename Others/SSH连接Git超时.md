有两种解决方法：

* `/etc/ssh/ssh_config` 中修改全局配置
* 在用户主目录下.ssh/中添加配置文件

解决办法以方法二为主

## 连接到 Github

```
Host github.com
Hostname ssh.github.com
Port 443
```

## 连接到 bitbucket

```
Host bitbucket.org
Hostname altssh.bitbucket.org
Port 443
```

# 参考链接

* [关于使用Git时push-pull超时-以及Github访问慢的解决办法](https://jasonkayzk.github.io/2019/10/10/关于使用Git时push-pull超时-以及Github访问慢的解决办法/)
* [ssh: connect to host github.com port 22: Connection timed out](https://stackoverflow.com/a/52817036)
* [ssh: connect to host bitbucket.org port 22: Connection timed out](https://stackoverflow.com/a/46492615)
* [https://gist.github.com/goddoe/5692bfc3cdc374d918a87882963edf36](https://gist.github.com/goddoe/5692bfc3cdc374d918a87882963edf36)
