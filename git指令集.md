# Git 指令集

这份文档不是只告诉你“命令怎么敲”，而是尽量讲清楚：

- 这条命令是干什么的
- 为什么要这样做
- 什么时候该用


---

## 一、先记住：Git 到底在管什么？

可以把 Git 理解成一个“会记版本的存档系统”。

比如在写一个项目：

- 第一天，项目能跑
- 第二天，你改了很多代码，结果改崩了
- 第三天，你想回到“昨天还能跑”的状态

Git 的作用，就是帮你把每个关键状态保存下来，并且能随时回看、对比、恢复、分支开发。


> Git 不是单纯保存文件，而是在保存“项目在某个时刻的完整状态”，就像游戏存档。

---

## 二、Git 最重要的核心模型：三区结构

Git 最容易让人头大的地方，就是它不是“改完直接提交”，而是分成 3 个区域：

### 1. 工作区（Working Directory）

就是你眼前正在修改的文件。

比如你正在改：

- `README.md`
- `login.js`

此时这些改动先存在工作区。

### 2. 暂存区（Stage / Index）

可以理解成：

> “我准备把这些改动放进下一次提交里。”

也就是说，暂存区不是最终存档，而是“准备提交的候选清单”。

### 3. 本地仓库（Repository）

这里保存的是真正已经提交过的版本历史，也就是 `commit`。

---

## 三、为什么 Git 要多一个“暂存区”？


因为 Git 想让你能精确控制“这次提交到底包含哪些内容”。

比如你今天做了两件事：

1. 修了登录 bug
2. 顺手改了几个按钮颜色

这两件事最好不要混在一次提交里，因为以后看历史会很乱。

你更希望这样：

- 第一次提交：只提交登录 bug 修复
- 第二次提交：只提交按钮样式修改

这时候暂存区就派上用场了。

也就是说：

> 暂存区的意义，是让你把提交拆得更清楚，而不是一股脑全塞进去。

---

## 四、最常用的标准流程

大多数时候，你写 Git 就是在重复下面这套流程：

```bash
git status
git add .
git commit -m "写清楚这次改了什么"
```

但你不要只记命令，要理解它们各自的角色：

1. `git status`
   看看现在改了什么，心里有数再操作
2. `git add .`
   把这次想提交的改动放进暂存区
3. `git commit -m "..."`
   正式生成一个版本记录

---

## 五、基础命令详解

### 1. `git init`

```bash
git init
```

作用：把当前文件夹初始化成一个 Git 仓库。

为什么要这样做：Git 不是默认生效的。你得先告诉它，“从现在开始，请帮我管理这个目录的版本”。

例子：

```bash
mkdir my-project
cd my-project
git init
```

这时候 `my-project` 就开始被 Git 管理了。

新手常见误区：

- `git init` 只需要在项目最外层目录执行一次
- 不要在子文件夹里反复 `git init`

---

### 2. `git status`

```bash
git status
```

作用：查看当前仓库状态。

它会告诉你：

- 哪些文件被修改了
- 哪些文件还没暂存
- 哪些文件已经暂存了
- 当前在哪个分支

为什么重要：因为 Git 很像一个“状态机”，操作前不看状态，很容易以为自己提交了，其实没提交。

例子：

```bash
git status
```

如果看到：

```bash
modified: app.js
modified: README.md
```

意思就是这两个文件已经改了，但还没进入暂存区。

建议：每次 `add` 前看一次，每次 `commit` 前再看一次。

---

### 3. `git add`

```bash
git add 文件名
git add .
```

作用：把工作区中的改动加入暂存区。

为什么不能直接提交：因为 Git 的设计理念是“先挑选这次要提交哪些改动，再生成提交记录”。

例子 1，只暂存一个文件：

```bash
git add login.js
```

意思是：我这次提交先只管 `login.js`，别的文件先不动。

例子 2，全部加入暂存区：

```bash
git add .
```

这表示当前目录下所有改动都加入暂存区。

为什么很多人会误用它：因为 `git add .` 很方便，但也最容易把调试代码、临时文件、不想提交的配置一起带进去。

所以在敲 `git add .` 之前，最好先跑一次：

```bash
git status
```

进阶一点：

```bash
git add -p
```

这个命令可以按“代码块”选择要不要暂存。意思是同一个文件里，你甚至可以只提交一部分改动。

---

### 4. `git commit`

```bash
git commit -m "修复登录接口报错"
```

作用：把暂存区的内容正式保存成一个提交记录，也就是一个 `commit`。

为什么它重要：因为每次 commit 都像是你项目历史上的一个“存档点”。以后你可以回看、对比、回退。

例子：

```bash
git add login.js
git commit -m "修复登录密码为空时报错"
```

这时候 Git 就记住了这次修复。

为什么提交说明不能乱写：因为几天后你自己都会忘。

不推荐：

```bash
git commit -m "改一下"
git commit -m "更新"
```

推荐：

```bash
git commit -m "修复登录密码为空时报错"
git commit -m "新增用户列表分页"
```

一个很实用的原则：提交信息要能回答“这次提交完成了什么事情”。

---

### 5. `git diff`

```bash
git diff
git diff --staged
```

作用：查看差异，看具体改了啥代码。

`git diff` 看的是工作区和暂存区之间的差异，也就是“你改了，但还没 add 的内容”。

`git diff --staged` 看的是暂存区和上一次提交之间的差异，也就是“你已经准备提交的内容到底是什么”。

为什么新手一定要会：因为很多时候你以为自己改的是 A，结果实际改进去的是 A+B+C。

推荐流程：

```bash
git diff
git add .
git diff --staged
```

这样你能在提交前做两次检查。

---

### 6. `git log`

```bash
git log
git log --oneline
```

作用：查看提交历史。

`git log` 信息更全：谁提交的、邮箱、时间、完整的提交说明、一长串哈希码，`git log --oneline` 更适合快速浏览：短哈希码、提交说明

例子：

```bash
git log --oneline
```

可能会看到

```bash
a1b2c3d 修复登录密码为空时报错
d4e5f6g 新增用户列表分页
h7i8j9k 初始化项目
```

这就像项目的时间线。

---

## 六、分支：为什么不建议直接在 `main` 上乱改

分支可以理解成一条独立开发路线。

如果你直接在 `main` 上改，风险是：

- 改到一半不能用
- 试验性代码污染主线
- 和别人协作时容易冲突

所以更推荐：

- `main` 保持稳定
- 新功能、新修复放到新分支做

---

### 1. `git branch`

```bash
git branch
git branch dev
```

作用：

- `git branch` 查看当前有哪些分支
- `git branch dev` 创建一个叫 `dev` 的分支

例子：

```bash
git branch login-feature
```

注意：这只是创建，还没有切过去。

---

### 2. `git switch`

```bash
git switch dev
git switch -c dev
```

作用：

- `git switch dev` 切换到已有分支
- `git switch -c dev` 创建并切换到新分支

例子：

```bash
git switch -c fix-login-bug
```

意思是：

- 新建一个 `fix-login-bug` 分支
- 并立刻切换过去

从这之后，你的改动就先发生在这个分支上，不会直接污染 `main`。

---

### 3. `git merge`

```bash
git merge dev
```

作用：把另一个分支的改动合并到当前分支。

例子：

```bash
git switch main
git merge dev
```

意思是把 `dev` 的成果并入 `main`。

最容易搞反的一点：`git merge dev` 不是“切到 dev”，而是“把 dev 合并到我当前所在的分支”。

---

## 七、远程仓库：为什么本地提交了，GitHub 还是没变化？

因为 `git commit` 只是提交到本地仓库，不会自动上传到远程平台。

常见远程平台有：

- GitHub
- GitLab
- Gitee

---

### 1. `git remote add origin URL`

```bash
git remote add origin 仓库地址
```

作用：给当前本地仓库绑定一个远程仓库地址。

例子：

```bash
git remote add origin https://github.com/yourname/demo.git
```

意思是以后这个本地仓库，就知道要和哪个远程仓库通信了。

---

### 2. `git push`

```bash
git push origin main
```

作用：把本地分支的提交推送到远程仓库。

为什么很多新手会困惑：因为他已经 `commit` 了，却发现 GitHub 页面没变化。原因是 `commit` 只是存到本地，`push` 才是上传到远程。

例子：

```bash
git push origin main
```

第一次推送常见写法：

```bash
git push -u origin main
```

这里的 `-u` 是建立跟踪关系。以后通常直接写 `git push` 就够了。

#### origin 就是远程仓库的别名
它不是固定名字，只是 Git 默认给你克隆 / 关联的远程仓库起的简称，真正完整地址是类似：https://github.com/xxx/xxx.git用 origin 代替，命令更短、更好记。简单说：origin = 你的 GitHub/GitLab 远程仓库

#### main 是什么？
例子：
```bash
git push origin main
```
main 是本地分支名，也是远程分支名。它是 Git 默认的主分支（以前叫 master，现在统一叫 main）。
意思是：把本地 main 分支的代码，推送到远程仓库的 main 分支你也可以推其他分支，比如：
```bash
git push origin dev
```
就是推 dev 分支。

#### 为什么第一次要加 -u？
第一次推送常见写法：
```bash
git push -u origin main
```
`-u = --set-upstream`
作用：建立本地分支与远程分支的跟踪关联。加了 -u 之后，Git 就记住了：本地 `main` ↔ 远程 `origin/main`
以后再推送，不用写 origin 和 main，直接：
```bash
git push
```
Git 就知道推到哪里去。不加 `-u`，每次都必须写完整：
```bash
git push origin main
```
---

### 3. `git pull`

```bash
git pull
```

作用：把远程最新内容拉到本地，并尝试合并。

你可以简单理解为：

```bash
git fetch + git merge
```

为什么要先 pull：如果你和别人一起开发，远程仓库可能已经有别人新的提交。你不先更新就直接 push，可能会失败，甚至引发冲突。

实用习惯：

- 开始工作前先 `git pull`
- 提交前先看 `git status`
- 推送前确认自己在正确分支

### 4. 使用 SSH 方式绑定远程仓库

```bash
git remote add origin git@github.com:yourname/demo.git
```

作用：和 HTTPS 方式一样，都是给本地仓库绑定远程地址，只是使用了 SSH 协议进行通信。

例子：

```bash
git remote add origin git@github.com:yourname/demo.git
```

意思是以后这个本地仓库，将通过 SSH 协议与远程仓库通信。

---

#### 为什么要使用 SSH

HTTPS 方式：

```bash
https://github.com/yourname/demo.git
```

SSH 方式：

```bash
git@github.com:yourname/demo.git
```

区别在于认证方式不同：

- HTTPS：用户名 + Token（或密码）
- SSH：公钥 + 私钥

---

#### SSH 的优势

1. 不需要反复输入密码

```bash
git push
```

SSH 会自动使用本地私钥完成认证。

---

2. 更安全

SSH 使用非对称加密：

- 公钥放在远程平台（如 GitHub）
- 私钥保存在本地

认证过程不需要传输密码。

---

3. 更适合自动化环境

例如：

- 服务器部署
- CI/CD 流程

SSH 可以在无交互情况下完成认证。

---

#### 使用 SSH 前需要做的配置

1. 生成密钥

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

---

2. 查看公钥

```bash
cat ~/.ssh/id_ed25519.pub
```

将输出内容复制到远程平台（如 GitHub）的 SSH Keys 设置中。

---

3. 测试连接

```bash
ssh -T git@github.com
```

如果配置成功，会返回认证成功信息。

---

#### 已有远程仓库，如何切换为 SSH

如果之前使用的是 HTTPS：

```bash
git remote set-url origin git@github.com:yourname/demo.git
```

作用：修改远程仓库地址为 SSH，不需要重新添加 remote。

---

#### 小结

- `git remote add` 本质是记录远程仓库地址
- HTTPS 和 SSH 只是通信方式不同
- 工程实践中通常推荐使用 SSH

---

## 八、撤销与回退：最容易害怕的一部分

先记住一句话：要先分清楚，自己想撤销的是工作区、暂存区，还是已经提交的版本。

---

### 1. 撤销暂存：`git restore --staged`

```bash
git restore --staged 文件名
```

作用：把文件从暂存区撤下来，但不丢失你实际改的内容。

例子：

```bash
git add .
git restore --staged config.js
```

效果是：

- `config.js` 不再处于暂存状态
- 但文件里的修改还在

这条命令相对安全，因为它只是“取消加入本次提交”，不是删除你的修改。

---

### 2. 撤销工作区修改：`git restore`

```bash
git restore 文件名
```

作用：把工作区文件恢复到最近一次提交的状态。

这意味着：对这个文件还没提交的修改，会被丢掉。

例子：

```bash
git restore app.js
```

如果你不确定，就先跑：

```bash
git diff
```

看清楚再操作。

---

### 3. 回退版本：`git reset`

```bash
git reset --soft HEAD~1
git reset --hard HEAD~1
```

它的作用是把当前分支指针移动到更早的提交。

#### `git reset --soft HEAD~1`

意思是：撤销最近一次提交，但把改动保留下来。

适合这种情况：

- 你刚 commit 了
- 但提交信息写错了
- 或者这次提交想重新整理一下

这是相对安全的回退。

#### `git reset --hard HEAD~1`

意思是：撤销最近一次提交，并且把相关改动也一起丢掉。

这是危险操作。

只有在非常确定这些改动完全不要了的时候，才建议用它。否则尽量别上来就用 `--hard`。

---

## 九、一个完整例子：从改代码到提交上传

假设你今天要修复一个按钮点击没反应的问题。

### 第 1 步：先看状态

```bash
git status
```

为什么：先确认仓库是不是干净的，避免把昨天没处理完的改动混进来。

### 第 2 步：修改代码

你改了：

- `button.js`
- `style.css`

### 第 3 步：检查改了什么

```bash
git diff
```

为什么：确认有没有误删代码、有没有留下调试代码。

### 第 4 步：加入暂存区

```bash
git add button.js
git add style.css
```

为什么不直接 `git add .`：因为你明确知道这次只想提交这两个文件，范围越清楚，历史越干净。

### 第 5 步：再检查一次准备提交的内容

```bash
git diff --staged
```

### 第 6 步：正式提交

```bash
git commit -m "修复按钮点击无响应问题"
```

### 第 7 步：推送到远程

```bash
git push origin main
```

如果你当前不是 `main`，就把 `main` 换成你自己的分支名。

---

## 十、速查表

### 查看状态

```bash
git status
```

用途：先确认现在仓库是什么状态。

### 查看差异

```bash
git diff
git diff --staged
```

用途：提交前检查改动。

### 加入暂存区

```bash
git add 文件名
git add .
```

用途：决定这次提交要包含什么。

### 提交

```bash
git commit -m "本次修改说明"
```

用途：生成一个版本存档点。

### 看历史

```bash
git log --oneline
```

用途：快速看之前做过什么。

### 新建并切换分支

```bash
git switch -c 新分支名
```

用途：不要直接在主分支乱改。

### 合并分支

```bash
git merge 分支名
```

用途：把另一个分支成果并过来。

### 推送到远程

```bash
git push
```

用途：把本地提交同步到远程仓库。

### 拉取远程更新

```bash
git pull
```

用途：先同步团队最新代码。

### 取消暂存

```bash
git restore --staged 文件名
```

用途：把文件从“准备提交名单”里拿出来。

### 放弃工作区修改

```bash
git restore 文件名
```

用途：不要这个文件的未提交改动了。

---

## 十一、最应该养成的习惯

1. 提交前先看 `git status`
2. 提交前先看 `git diff`
3. 一次提交只做一件事
4. 尽量别直接在 `main` 上开发
5. 谨慎使用 `reset --hard`

---

## 十二、流程总结

> 改文件在工作区，挑选提交内容放进暂存区，`commit` 形成版本记录，`push` 才会同步到远程。


---

## 十三、日常操作模板

```bash
git pull
git status
git diff
git add 文件名
git diff --staged
git commit -m "清楚描述本次修改"
git push
```
这套流程的核心思想是：先同步，再确认，再暂存，再检查，再提交，最后推送。