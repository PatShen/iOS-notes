# How to use proxy in command line

```shell
# In the file .zshrc or .profile
alias proxy='http_proxy="http://domain:port"; HTTP_PROXY="http://domain:port"; https_proxy="http://domain:port"; HTTPS_PROXY="http://domain:port"'
```

## Usage

* Use proxy

```shell
$ proxy pod install
```

* Without proxy

```shell
pod install
```
