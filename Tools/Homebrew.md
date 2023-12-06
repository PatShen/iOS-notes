# 查看配置

```
brew config
```

# 替换源
## 中科大镜像
### brew.git

```
cd "$(brew --repo)" && git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
```

### homebrew-core.git

```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core" && git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

### homebrew-cask

```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask" && git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

### 替换 homebrew-bottles 镜像

```
echo 'export HOMEBREW_BOTTLE_DOMAN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
```

## 官方镜像
### brew.git

```
cd "$(brew --repo)" && git remote set-url origin https://github.com/Homebrew/brew
```

### homebrew-core.git

```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core" && git remote set-url origin https://github.com/Homebrew/homebrew-core
```

### homebrew-cask

```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask" && git remote set-url origin https://github.com/Homebrew/homebrew-cask
```

注释掉 ~/.zshrc ⏰定义的 `HOMEBREW_BOTTLE_DOMAN`
