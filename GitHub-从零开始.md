# GitHub 从零开始

> 这份文档对应的练习仓库就在 `D:\deep\git`，里面已经有 3 次真实提交、1 个分支和 1 次合并。
> 边看边在这里敲命令，比只读文档快十倍。

---

## 一、先搞懂一件事：Git 和 GitHub 不是同一个东西

| | 是什么 | 在哪 |
|---|---|---|
| **Git** | 版本控制工具，一个本地程序 | 你电脑上 |
| **GitHub** | 托管 Git 仓库的网站 | 云端 |

Git 负责"记录每一次改动"，GitHub 负责"把记录放到网上，方便备份和协作"。
**没有 GitHub，Git 照样能用**；没有 Git，GitHub 什么都干不了。

---

## 二、心智模型：三个区

这是所有 Git 命令的骨架。记住这张图，80% 的困惑会消失：

```
 工作区                暂存区               本地仓库            远程仓库
（你能看到的文件）  （准备提交的清单）  （永久历史记录）    （GitHub 上）
     |                    |                   |                  |
     |--- git add ------->|                   |                  |
     |                    |--- git commit --->|                  |
     |                    |                   |--- git push ---->|
     |<------------------- git restore -------|                  |
     |<----------------------------------------- git pull -------|
```

- **工作区**：你正在编辑的文件
- **暂存区（stage / index）**：你挑出来"这次要提交"的改动
- **本地仓库**：`git commit` 之后进入历史，永久留存
- **远程仓库**：`git push` 之后到了 GitHub

`git status` 之所以最常用，就是因为它告诉你"现在东西在哪个区"。

---

## 三、七天路线

### Day 0 · 装好工具（已完成 ✅）

- Git 2.55 已安装在 `C:\Program Files\Git\cmd\git.exe`
- 全局身份已配置：`user.name` / `user.email`
- **注意**：装完后要关掉终端重开，新开的窗口才认得 `git` 命令

验证：

```powershell
git --version
git config --global --list
```

> ⚠️ 现在配的邮箱 `weixin@example.com` 是占位用的。等你要往 GitHub 推送时，
> 换成你 GitHub 账号的邮箱，否则提交记录不会算在你名下：
> `git config --global user.email "你的邮箱"`

---

### Day 1 · 提交循环（日常 90% 的操作用的就是这四条）

```powershell
git status                 # 我现在有什么改动？
git add 文件名             # 把改动放进暂存区（git add . 是全部）
git commit -m "说明"       # 存进历史
git log --oneline          # 看看历史长什么样
```

练习：随便改一行 `README.md` → `git status` → `git add` → `git commit` → `git log`。

**提交信息怎么写**：说清楚"为什么改"，不是"改了什么"。

- ❌ `修改`、`update`、`fix bug`
- ✅ `README 增加进度清单`
- ✅ `修复登录失败：密码字段被 trim 掉了空格`

---

### Day 2 · 看历史 + 反悔（新手最需要的安全感）

```powershell
git diff                      # 还没暂存的改动，到底改了啥
git diff --staged             # 已暂存、马上要提交的改动
git log --oneline --graph --all   # 图形化看分支和提交
git show HEAD~1:README.md     # 看上一版文件的内容
```

三种"反悔"，按严重程度递增：

```powershell
git restore 文件名            # ① 丢弃工作区改动（危险，不可恢复）
git restore --staged 文件名   # ② 从暂存区拿出来，改动还在
git reset --soft HEAD~1       # ③ 撤销上一次提交，改动退回暂存区
```

> 只要改动**提交过**，就永远能找回。真正会丢的只有"从没 add、从没 commit"的改动。

---

### Day 3 · 分支（Git 最值钱的功能）

分支 = 平行世界。在分支上乱搞，`main` 毫发无伤。

```powershell
git switch -c feature/plan    # 新建并切换（-c = create）
git switch main               # 切回 main
git branch                    # 列出本地分支，带 * 的是当前
git merge feature/plan        # 把分支合并进当前分支
git branch -d feature/plan    # 删掉已合并的分支
```

**练习时亲眼看到的现象**：在 `feature/plan` 上新建 `plan.md` 并提交，切回 `main` 后
`plan.md` 会**凭空消失**——因为它只存在于那个分支。合并后才会出现。

这就是为什么"改大功能前先开分支"是铁律。

---

### Day 4 · 注册 GitHub + 建第一个远程仓库

1. 打开 <https://github.com/signup>，用邮箱注册（记住用户名，它会出现在你的主页地址里）
2. 邮箱验证 → 完善资料
3. 右上角 **+** → **New repository**
   - Repository name：`my-first-repo`
   - 选 **Public**（私有仓库也免费，但公开的以后能当作品集）
   - **不要**勾选 "Add a README file"（本地已经有内容了，勾了会造成第一次推送冲突）

---

### Day 5 · 配 SSH 密钥 + 第一次推送

**为什么用 SSH**：配一次，以后 `push` / `pull` 再也不用输密码。

```powershell
# 1) 生成密钥（一路回车，密码可留空）
ssh-keygen -t ed25519 -C "你的邮箱"

# 2) 复制公钥内容
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
```

3. 打开 <https://github.com/settings/keys> → **New SSH key** → 粘贴 → 保存

```powershell
# 4) 验证连接（第一次会问 yes/no，输 yes）
ssh -T git@github.com
# 看到 "Hi 用户名! You've successfully authenticated" 就成功了
```

**把本地仓库接上远程**：

```powershell
git remote add origin git@github.com:你的用户名/my-first-repo.git
git branch -M main
git push -u origin main
```

`-u` 只需要第一次加，它记住"本地 main 对应远程 main"，以后直接 `git push`。

**之后的日常**：

```powershell
git pull      # 先拉，避免冲突
git push      # 再推
```

---

### Day 6 · 协作三件套：Fork / Issue / Pull Request

这是 GitHub 真正区别于"网盘备份"的地方。

| 概念 | 干什么 |
|---|---|
| **Issue** | 提问题、记待办、报 bug。每个 Issue 有编号，提交信息里写 `fix #12` 关联 |
| **Fork** | 把别人的仓库复制一份到你名下（你没有写权限时用） |
| **Pull Request (PR)** | 请求把你的分支合并进目标仓库。代码审查就发生在 PR 里 |

**标准协作流程**（以后进公司也是这套）：

```powershell
git switch -c fix/login-bug      # 1. 从最新 main 开分支
# ...改代码...
git add . && git commit -m "修复登录失败"
git push -u origin fix/login-bug # 2. 推到远程
# 3. 网页上点 "Compare & pull request"，写清楚改了什么、为什么
# 4. 等 review → 通过后点 Merge
git switch main && git pull      # 5. 本地同步合并结果
```

---

### Day 7 · 冲突：不用怕，它是正常的

冲突只在**同一个文件的同一区域被两边同时改**时发生。

```powershell
git pull
# 提示 CONFLICT (content): Merge conflict in README.md
```

打开文件会看到：

```
<<<<<<< HEAD
你这边的版本
=======
远程的版本
>>>>>>> origin/main
```

处理方式：**删掉 `<<<<<<<`、`=======`、`>>>>>>>` 三行标记，把内容改成你最终想要的样子**，然后：

```powershell
git add README.md
git commit            # 不带 -m，git 会给你一个默认的合并提交信息
```

**记住：冲突不是错误，是 Git 在问你"这两份改动你要哪个"。**

---

## 四、命令速查表

| 我想…… | 命令 |
|---|---|
| 看现在什么状态 | `git status` |
| 看改了什么 | `git diff` |
| 看提交历史 | `git log --oneline --graph --all` |
| 暂存全部改动 | `git add .` |
| 提交 | `git commit -m "说明"` |
| 撤销工作区改动 | `git restore 文件名` |
| 撤销暂存 | `git restore --staged 文件名` |
| 新建分支并切换 | `git switch -c 分支名` |
| 切分支 | `git switch 分支名` |
| 合并分支 | `git merge 分支名` |
| 拉取远程更新 | `git pull` |
| 推送 | `git push` |
| 看远程地址 | `git remote -v` |
| 临时存一下手头改动 | `git stash` / `git stash pop` |

---

## 五、新手必踩的 8 个坑

1. **在错误的目录里 `git init`** —— 先 `pwd` / `Get-Location` 确认位置
2. **忘了 `git status`** —— 它是你的眼睛，每次 add / commit 前都看一眼
3. **一次提交干了五件事** —— 出问题时无法单独回滚，一件事一次提交
4. **把密码、密钥提交上去** —— 用 `.gitignore` 挡掉 `.env`；已经提交了就换密钥，别只删文件
5. **`git push` 前不 `git pull`** —— 多人协作时冲突会更多
6. **害怕分支** —— 分支是免费的，`main` 弄脏了才麻烦
7. **`git reset --hard` 乱用** —— 这是少数真正会丢东西的命令
8. **以为 GitHub 等于备份** —— 它更是"协作和展示"，只 commit 不 push 等于没备份

---

## 六、你这个仓库现在长这样

```
* 459d021 新增学习计划              ← merge 后的 main
* ff6a9bd README 增加进度清单
* 71ac41f 初始提交：添加 README 和 .gitignore
```

文件：`.gitignore`、`README.md`、`plan.md`

**下一步就两件事**：
1. 注册 GitHub 账号（Day 4）
2. 配 SSH、`git push`（Day 5）—— 做完这两步，你就"会用 GitHub"了，剩下的都是熟练度
