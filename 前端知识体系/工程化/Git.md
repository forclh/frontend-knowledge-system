# Git 常用命令
## 配置相关

### `git config`
```bash
git config --global user.name "Your Name"          # 设置全局用户名
git config --global user.email "email@example.com" # 设置全局邮箱
git config --list                                  # 查看所有配置
```
- `--global`：全局配置（保存在 `~/.gitconfig`）
- `--local`：仅针对当前仓库（默认选项）

---

## 仓库初始化与克隆

### `git init`
```bash
git init [project-name]  # 初始化新仓库
```

### `git clone`
```bash
git clone <url> [local-name]  # 克隆远程仓库
```
- `--depth 1`：浅克隆（仅克隆最新提交，节省时间）

---

## 基本操作

### `git add`
```bash
git add <file>       # 添加指定文件到暂存区
git add .            # 添加所有修改（包括新文件和修改的文件）
git add -A           # 添加所有修改（包括删除操作）
git add -p           # 交互式选择修改片段
```

### `git commit`
```bash
git commit -m "message"    # 提交暂存区的修改
git commit -a -m "message" # 跳过 `git add`，直接提交已跟踪文件的修改
```
- `-a`：自动添加已跟踪文件的修改（不包含新文件）
- `--amend`：修改最后一次提交（可修改提交信息或内容）

### `git status`
```bash
git status     # 查看工作区和暂存区状态
git status -s  # 简洁模式输出
```

---

## 分支管理

### `git branch`
```bash
git branch                  # 查看本地分支
git branch <branch-name>    # 创建新分支
git branch -d <branch-name> # 删除已合并的分支
git branch -D <branch-name> # 强制删除未合并的分支
```

### `git checkout`
```bash
git checkout <branch-name>      # 切换到分支
git checkout -b <branch-name>   # 创建并切换到新分支
git checkout <file>             # 丢弃工作区中文件的修改
```

### `git merge`
```bash
git merge <branch-name>  # 合并指定分支到当前分支
git merge --abort        # 终止合并冲突
```

### `git rebase`
```bash
git rebase <branch-name>  # 将当前分支的提交变基到目标分支
git rebase --abort        # 终止变基冲突
```

---

## 远程操作

### `git remote`
```bash
git remote -v               # 查看远程仓库地址
git remote add <name> <url> # 添加远程仓库
git remote remove <name>    # 删除远程仓库
```

### `git push`
```bash
git push origin <branch-name>    # 推送本地分支到远程
git push -u origin <branch-name> # 推送并关联上游分支（后续可省略分支名）
git push --force                 # 强制推送（覆盖远程历史，慎用！）
```

### `git pull`
```bash
git pull origin <branch-name>  # 拉取远程分支并合并（= fetch + merge）
git pull --rebase              # 拉取并使用变基合并（更干净的提交历史）
```

### `git fetch`
```bash
git fetch origin          # 下载远程所有更新（不自动合并）
git fetch origin <branch> # 拉取指定远程分支的更新
```

---

## 撤销与清理

### `git reset`
```bash
git reset HEAD <file>      # 取消暂存区的文件修改（保留工作区）
git reset --soft HEAD^     # 撤销最后一次提交（保留修改到暂存区）
git reset --mixed HEAD^    # 撤销提交和暂存（保留工作区修改，默认行为）
git reset --hard HEAD^     # 彻底丢弃最后一次提交和所有修改（慎用！）
```
- `HEAD^` 表示前一次提交，`HEAD~n` 表示前 n 次提交。

### `git revert`
```bash
git revert <commit-id>  # 创建一个新提交来撤销指定提交的更改（安全撤销）
```

### `git clean`
```bash
git clean -df  # 删除未被跟踪的文件和目录
```
- `-d`：包含目录
- `-f`：强制操作
- `-n`：预览将要删除的文件（不实际执行）

---

## 标签管理

### `git tag`
```bash
git tag                    # 查看所有标签
git tag v1.0.0             # 创建轻量标签
git tag -a v1.0.0 -m "msg" # 创建附注标签
git push origin --tags     # 推送所有标签到远程
```

---

## 查看历史与差异

### `git log`
```bash
git log --oneline          # 简洁的单行显示
git log --graph            # 图形化显示分支合并历史
git log -p                 # 显示提交的详细差异
git log -n 5               # 显示最近5条提交
```

### `git diff`
```bash
git diff                   # 工作区与暂存区的差异
git diff --staged          # 暂存区与最新提交的差异
git diff <commit1> <commit2> # 比较两次提交的差异
```

---

## 暂存与恢复（Stash）

### `git stash`
```bash
git stash                  # 暂存当前工作区修改
git stash list             # 查看暂存列表
git stash apply stash@{n}  # 恢复指定暂存（不删除暂存记录）
git stash pop              # 恢复最近一次暂存并删除记录
git stash drop stash@{n}   # 删除指定暂存记录
```

---

## 高级操作

### `git cherry-pick`
```bash
git cherry-pick <commit-id>  # 将指定提交应用到当前分支
```

### `git bisect`
```bash
git bisect start   # 启动二分查找（用于定位引入Bug的提交）
git bisect good    # 标记当前提交为“正常”
git bisect bad     # 标记当前提交为“有问题”
```

---

## 注意事项
1. **谨慎使用 `--force` 或 `--hard`**：可能导致数据丢失。
2. **分支管理策略**：推荐主分支（如 `main`）保护，通过 Pull Request 合并代码。
3. **提交信息规范**：使用清晰的前缀（如 `feat:`, `fix:`, `docs:`）方便追踪。