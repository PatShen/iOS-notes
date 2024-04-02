# Version 1.13.0 报错

```
undefined method `deprecator' for ActiveSupport:Module (NoMethodError)
Did you mean?  deprecate_constant
from /Users/user-name/.rvm/gems/ruby-3.0.6/gems/activesupport-7.1.0/lib/active_support/core_ext/array/conversions.rb:8:in `<top (required)>'
...
```
原因是 activesupport 中的某个方法不建议使用了，暂时的解决办法是将7.1.0卸载，安装它的7.0.8版本。

```
gem uninstall activesupport -v 7.1.0
gem install activesupport -v 7.0.8
```

https://stackoverflow.com/questions/77236339/after-updating-cocoapods-to-1-13-0-it-throws-error

# Cocoapods 细节

https://www.cnblogs.com/Jcloud/p/17719358.html
