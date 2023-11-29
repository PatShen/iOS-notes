# RVM

## 创建默认 ruby 版本

```
$ rvm alias create default ruby-2.7.2
% rvm --default use 2.1.1
```

## 设置路径

```
# Add RVM to PATH for scripting. Make sure this is the last PATH variable change.
export PATH="$PATH:$HOME/.rvm/bin"
```
```
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*
```

## Bundle install 报错

```
Could not load OpenSSL.
You must recompile Ruby with OpenSSL support or change the sources in your Gemfile from 'https' to 'http'. Instructions for compiling with OpenSSL using RVM are available at rvm.io/packages/openssl.
```
解决方法：
```
rvm install 3.0.6 --with-openssl-dir=$(which openssl)
```

## 在Apple Silicon电脑中安装成功，但无法正常使用 cocoapods

```
# 安装 ruby 时的错误
Error running '__rvm_make install',
please read /Users/{username}/.rvm/log/1701256414_ruby-2.7.7/install.log
There has been an error while running make install. Halting the installation.
```
虽然安装后能安装cocoapods等工具，但运行 pod install 时会提示错误

```
# pod install 时的错误
/Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi.rb:5:in `require': dlopen(/Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi_c.bundle, 0x0009): tried: '/Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi_c.bundle' (mach-o file, but is an incompatible architecture (have 'x86_64', need 'arm64')), '/System/Volumes/Preboot/Cryptexes/OS/Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi_c.bundle' (no such file), '/Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi_c.bundle' (mach-o file, but is an incompatible architecture (have 'x86_64', need 'arm64')) - /Users/{username}/.rvm/gems/ruby-2.7.7/gems/ffi-1.16.3/lib/ffi_c.bundle (LoadError)
```

解决办法：
```
rvm install 3.2.2 --with-openssl-dir=$(brew --prefix openssl@1.1) --force
```
参考链接：
* https://github.com/rvm/rvm/issues/5285#issuecomment-1746915169
* https://github.com/rvm/rvm/issues/5246#issuecomment-1671324137

## ThreadError
```
/Users/{username}/.rvm/rubies/ruby-2.7.7/lib/ruby/site_ruby/2.7.0/rubygems/core_ext/kernel_gem.rb:67:in `synchronize': deadlock; recursive locking (ThreadError)
```
解决办法：
```
gem update --system
```
* https://stackoverflow.com/questions/67564557/kernel-gem-rb67in-synchronize-deadlock-recursive-locking-threaderror

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
https://www.jianshu.com/p/a97eca5254b8
https://rvm.io/rubies/default

# rbenv not changing ruby version

```
$ export PATH="$HOME/.rbenv/bin:$PATH"
$ eval "$(rbenv init -)"
$ rbenv global 2.7.7

$ ruby -v
```
https://stackoverflow.com/questions/10940736/rbenv-not-changing-ruby-version

# cannot load such file -- openssl (RVM)

* `bundle install`
```
Traceback (most recent call last):
   2: from /Users/username/.rvm/rubies/ruby-2.7.7/bin/bundle:23:in `<main>'
   1: from /Users/username/.rvm/rubies/ruby-2.7.7/lib/ruby/2.7.0/rubygems.rb:296:in `activate_bin_path'
/Users/username/.rvm/rubies/ruby-2.7.7/lib/ruby/2.7.0/rubygems.rb:277:in `find_spec_for_exe': Could not find 'bundler' (2.3.22) required by your /Users/username/Documents/xxxx/Gemfile.lock. (Gem::GemNotFoundException)
To update to the latest version installed on your system, run `bundle update --bundler`.
To install the missing version, run `gem install bundler:2.3.22`
```
* `gem install bundler:2.3.22`
```
ERROR:  Loading command: install (LoadError)
	cannot load such file -- openssl
ERROR:  While executing gem ... (NoMethodError)
    undefined method `invoke_with_build_args' for nil:NilClass
```
* Solution
```
rvm pkg install openssl
rvm reinstall all --force
```

https://stackoverflow.com/questions/15129355/ruby-2-0-rails-gem-install-error-cannot-load-such-file-openssl
