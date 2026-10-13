# Git 使用指南

**目录**

- [基本概念](#基本概念)
- [仓库操作](#仓库操作)
- [分支操作](#分支操作)
- [日常开发流程](#日常开发流程)
- [提交规范](#提交规范)
- [历史与对比](#历史与对比)
- [撤销操作](#撤销操作)
- [冲突解决](#冲突解决)
- [常用 Git 配置](#常用-git-配置)
- [VS Code 中操作 Git](#vs-code-中操作-git)
- [快速参考](#快速参考)

---

## 基本概念

Git 是分布式版本控制系统，核心工作流：

```
工作区 (Working Directory) → 暂存区 (Stage) → 本地仓库 (Local Repo) → 远程仓库 (Remote)
```

---

## 仓库操作

### 克隆远程仓库

```bash
git clone <仓库地址>
```

### 查看远程仓库信息

```bash
git remote -v
```

### 添加/修改远程仓库地址

```bash
git remote add origin <仓库地址>
git remote set-url origin <新仓库地址>
```

---

## 分支操作

### 查看分支

```bash
git branch          # 查看本地分支
git branch -r       # 查看远程分支
git branch -a       # 查看所有分支
```

### 创建与切换分支

```bash
git branch <分支名>            # 创建分支
git checkout <分支名>          # 切换分支
git checkout -b <分支名>       # 创建并切换到新分支
```

### 删除分支

```bash
git branch -d <分支名>         # 删除已合并的本地分支
git branch -D <分支名>         # 强制删除本地分支
git push origin -d <分支名>    # 删除远程分支
```

---

## 日常开发流程

### 标准工作流

```bash
git pull origin main           # 1. 拉取最新代码
git checkout -b feature/xxx    # 2. 创建功能分支
# ... 编写代码 ...
git status                     # 3. 查看变更状态
git add .                      # 4. 暂存所有变更
git commit -m "【类型】描述"    # 5. 提交
git push origin feature/xxx    # 6. 推送并提 Pull Request
```

### 状态查看

```bash
git status                     # 查看工作区与暂存区状态
git status -s                  # 简洁模式
git diff                       # 查看未暂存的改动
git diff --staged              # 查看已暂存待提交的改动
```

### 暂存与提交

```bash
git add <文件路径>             # 暂存指定文件
git add .                      # 暂存所有变更
git add -A                     # 暂存所有（含删除）
git commit -m "【类型】描述"    # 提交
git commit -a -m "【类型】描述" # 跳过暂存，直接提交已跟踪文件的变更
```

### 同步远程

```bash
git pull origin main           # 拉取并合并远程代码
git fetch origin               # 仅拉取，不合并
git push origin main           # 推送本地提交到远程
git push -u origin <分支名>    # 首次推送并建立跟踪关系
```

---

## 提交规范

提交信息格式：**`【类型】简短描述`**


| 类型     | 说明                         | 示例                          |
| ---------- | ------------------------------ | ------------------------------- |
| **新增** | 新增功能、新增文件           | `【新增】实现 PID 控制器模块` |
| **修复** | 修复 Bug 或逻辑错误          | `【修复】CAN 通信丢帧问题`    |
| **优化** | 优化代码结构或性能           | `【优化】减少 RAM 占用`       |
| **调整** | 调整配置、修改样式、修改文档 | `【调整】完善 Git 协作指南`   |
| **重构** | 重构代码逻辑                 | `【重构】底盘运动学解算模块`  |
| **删除** | 删除文件、废弃代码           | `【删除】旧版调试代码`        |

---



## 历史与对比

### 查看提交历史

```bash
git log                        # 完整历史
git log --oneline              # 单行简洁模式
git log --oneline -10          # 最近 10 条
git log --graph --all          # 图形化分支历史
```

### 查看具体改动

```bash
git diff                       # 工作区 vs 暂存区
git diff --staged              # 暂存区 vs 最新提交
git diff <commit-id>           # 当前 vs 某次提交
git diff HEAD~1                # 当前 vs 上一次提交
git show <commit-id>           # 查看某次提交的详细内容
```

### 查看某次提交的作者与时间

```bash
git blame <文件路径>           # 逐行显示修改来源
```

---

## 撤销操作

### 撤销工作区改动

```bash
git restore <文件路径>         # 丢弃工作区的改动（回到最近一次暂存或提交状态）
git restore .                  # 丢弃所有工作区改动
```

### 撤销暂存区

```bash
git restore --staged <文件路径>  # 取消暂存，改动保留在工作区
git restore --staged .           # 取消所有暂存
```

### 修正上一次提交

```bash
git commit --amend -m "【调整】新的提交信息"
```

> `--amend` 会修改提交历史，仅在**未推送**到远程或确认无人基于该提交时使用。

### 回退版本

```bash
git reset --soft HEAD~1        # 撤销提交，改动回到暂存区
git reset --mixed HEAD~1       # 撤销提交，改动回到工作区（默认）
git reset --hard HEAD~1        # 彻底丢弃最近一次提交的所有改动
```

> `--hard` 不可逆，使用前务必确认。

---

## 冲突解决

当 `git pull` 或 `git merge` 出现冲突时：

### 1. 在 VS Code 中解决

冲突文件会以不同颜色标记，点击以下选项：

- `Accept Current Change` — 保留自己的版本
- `Accept Incoming Change` — 采用远程的版本
- `Accept Both Changes` — 两者都保留
- `Compare Changes` — 并排对比

### 2. 手动解决

搜索冲突标记：

```
<<<<<<< HEAD
（自己的代码）
=======
（远程的代码）
>>>>>>> origin/main
```

手动编辑、删除标记后保存。

### 3. 完成合并

```bash
git add .
git commit -m "【调整】解决合并冲突"
```

---

## 常用 Git 配置

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git config --global core.autocrlf input    # Windows 下建议设为 true
git config --global pull.rebase false       # pull 时使用 merge 而非 rebase
```

---

## VS Code 中操作 Git


| 操作     | 方式                                      |
| ---------- | ------------------------------------------- |
| 查看变更 | 左侧 Source Control 面板 (`Ctrl+Shift+G`) |
| 暂存文件 | 点击文件旁的`+` 号                        |
| 提交     | 在输入框填写信息，`Ctrl+Enter`            |
| 推送     | 底部状态栏点击同步按钮                    |
| 拉取     | `Ctrl+Shift+P` → `Git: Pull`             |
| 解决冲突 | 在冲突文件中直接点击选项按钮              |

---

## 快速参考


| 命令                           | 作用             |
| -------------------------------- | ------------------ |
| `git clone <url>`              | 克隆仓库         |
| `git pull origin main`         | 拉取最新代码     |
| `git status`                   | 查看状态         |
| `git add .`                    | 暂存所有变更     |
| `git commit -m "【类型】描述"` | 提交             |
| `git push origin main`         | 推送到远程       |
| `git log --oneline`            | 查看历史         |
| `git diff`                     | 查看改动         |
| `git restore <file>`           | 撤销工作区改动   |
| `git restore --staged <file>`  | 取消暂存         |
| `git checkout -b <name>`       | 创建并切换分支   |
| `git branch -d <name>`         | 删除分支         |
| `git merge <branch>`           | 合并分支         |
| `git stash`                    | 暂存当前工作区   |
| `git stash pop`                | 恢复暂存的工作区 |
