# How to use proxy in command line

```shell
# In the file .zshrc or .profile
alias proxy='http_proxy="http://domain:port"; HTTP_PROXY="http://domain:port"; https_proxy="http://domain:port"; HTTPS_PROXY="http://domain:port"'
```

Usage

```shell
proxy pod install
```
