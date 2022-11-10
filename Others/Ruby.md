# RVM

## 创建默认 ruby 版本

```
$ rvm alias create default ruby-2.7.2
% rvm --default use 2.1.1
```

# Fastlane

环境变量

```
1. open ~/.zshrc
2. save path
   2.1 if you install fastlane with Homebrew
   Copy export PATH="$HOME/.fastlane/bin:$PATH" on the bottom line.
   2.2 if you install fastlane with RubyGems
   Copy export PATH="/usr/local/bin/fastlane"" on the bottom line.
3. save upon file and try fastlane init, everything is OK!
```

https://stackoverflow.com/a/52273420
