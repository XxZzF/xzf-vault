### 关闭 Git 的 CRLF/LF 自动换行符转换：
1. 局部关闭（仅当前仓库生效，推荐）：
打开 Git Bash（或终端），进入目标仓库目录（比如 Obsidian 仓库路径），输入：
```bash
git config core.autocrlf false
```

### 解决Git Bash的中文路径乱码
``` bash
git config --global core.quotepath false
```