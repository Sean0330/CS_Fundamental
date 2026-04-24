# Git 和 GitHub 学习笔记

## 一. 核心概念

### Repository
Repository 就是仓库，也就是一个项目的存放位置。

- **Public**：公开仓库，别人可以看到
- **Private**：私有仓库，只有你或被授权的人可以看到

这两种后面都可以改。

---

### commit
commit 可以理解为一次**提交记录**或一次**快照保存**。

它的作用是：

- 把暂存区里的内容正式保存到版本历史中
- 记录这次改了什么
- 方便以后查看、对比、回退

最常见流程：

```bash
git add .
git commit -m "说明这次改了什么"
git push
```

其中：

- `git add`：把当前版本放进暂存区
- `git commit`：正式提交暂存区内容
- `git push`：把本地提交传到 GitHub

---

### branch
branch 就是一条独立开发线。

你在哪个分支上 `commit`，记录就加到哪个分支上。

---

### Fork
Fork 不是普通的 branch。

Fork 的意思是：
把别人的整个项目复制一份到**自己的 GitHub 账号**下，形成一个独立仓库。  
之后你可以在自己复制出来的仓库里继续改，不会直接影响原项目。

---

### Pull Request
Pull Request 可以理解为“提交请求”。

常见过程是：

1. 先 Fork 别人的项目
2. 在你自己的仓库里修改
3. 修改完成后，向原项目发起 Pull Request
4. 原项目作者审核你的修改
5. 如果同意，就把你的修改合并进去

---

### Merge
Merge 可以理解为“合并”。

如果别人 Fork 了我们的项目，对其进行了修改，并且提出了 Pull Request，这时我们就可以审核这个请求。  
如果这个 Pull Request 满足要求，并且和原项目没有冲突，就可以合并到原项目中。  
当然，是否合并，由项目维护者决定。

---

### Watch
Watch 可以理解为“观察”或“关注项目更新”。

如果你 Watch 了一个项目，之后这个项目有更新时，你会收到通知。

---

## 二. 常用命令与关键返回

### `git init`

```bash
git init
```

作用：  
把当前目录初始化成一个 Git 仓库。

---

### `git status`

```bash
git status
```

作用：  
查看当前仓库状态。

关键返回 1：

```bash
fatal: not a git repository (or any of the parent directories): .git
```

意思：  
当前目录还不是 Git 仓库，或者你现在不在 Git 仓库目录里。

关键返回 2：

```bash
On branch master

No commits yet

Untracked files:
  demo.txt
```

意思：

- 当前在 `master` 分支
- 还没有任何提交记录
- `demo.txt` 还没有被 Git 跟踪

这里的 `Untracked files` 是重点，表示文件已经在目录里，但 Git 还没开始管理它。

关键返回 3：

```bash
Changes to be committed:
  new file:   demo.txt
```

意思：  
文件已经进入暂存区，下一次 `commit` 会提交它。

关键返回 4：

```bash
Changes not staged for commit:
  modified:   demo.txt
```

意思：  
文件已经改了，但这些改动还没有重新 `add`，下一次 `commit` 不会提交它。

---

### `git add`

```bash
git add demo.txt
```

作用：  
把文件当前内容放进**暂存区**。

最重要的一点：

**`git add` 不是提交。**  
它只是把“当前这一版”准备好，等下一次 `commit`。

如果想把当前目录下所有新增和修改的文件都加入暂存区：

```bash
git add .
```

---

### `git commit`

```bash
git commit -m "add demo file"
```

作用：  
把暂存区里的内容正式保存到版本历史中。

最重要的一点：

**`git commit` 提交的是暂存区内容，不是工作区里所有最新内容。**

关键返回：

```bash
[master fb53b40] add demo file
 1 file changed, 3 insertions(+)
```

意思：

- `master`：这次提交发生在 `master` 分支
- `fb53b40`：这次 commit 的短编号
- `1 file changed`：改了 1 个文件
- `3 insertions(+)`：新增了 3 行

如果在分支上提交，例如：

```bash
[feature-demo 5bc219b] update demo in feature branch
 1 file changed, 2 insertions(+)
```

意思就是：  
你已经在 `feature-demo` 分支上成功提交了一次修改，这次改了 1 个文件，新增了 2 行。

---

### `git log`

```bash
git log
```

作用：  
查看提交历史。

简洁写法：

```bash
git log --oneline
```

查看 branch 关系更常用的写法：

```bash
git log --oneline --decorate --graph --all
```

关键返回：

```bash
* 5bc219b (HEAD -> feature-demo) update demo in feature branch
* d95394e (master) add information for test commit and add
* fb53b40 add demo file
```

意思：

- `HEAD -> feature-demo`：你当前站在 `feature-demo`
- `(master)`：`master` 分支现在停在 `d95394e`
- `feature-demo` 比 `master` 多了一次提交

这里可以先把 `HEAD` 理解成：

**我当前所在的位置。**

---

### `cat`

```bash
cat demo.txt
```

作用：  
查看文件内容。

最重要的一点：

**`cat demo.txt` 看到的是工作区当前版本。**

不是暂存区版本，也不是最近一次提交版本。

如果要看另外两个版本：

```bash
git show :demo.txt
git show HEAD:demo.txt
```

记法：

```bash
cat demo.txt            # 工作区
git show :demo.txt     # 暂存区
git show HEAD:demo.txt # 最近一次提交
```

---

### `git branch`

```bash
git branch
```

作用：  
查看本地分支。

创建分支, 但不会自动切换过去：

```bash
git branch a
```

删除分支：

```bash
git branch -D a
```
删除远端分支：
```bash
git push origin --delete a
```
---

### `git switch`

```bash
git switch a
```

作用：  
切换到指定分支。

如果想创建并立即切换：

```bash
git switch -c feature-demo
```

关键返回：

```bash
Switched to a new branch 'feature-demo'
```

意思：  
新分支创建成功，并且已经切换过去了。

---

### `git merge`


#### 1. merge作用：把 feature-a 的改动并入 main：

```bash
git switch main
git merge feature-a
```
这里：
```text
当前分支 main = 接收方
feature-a = 被合并进来的分支
```
---

#### 2. merge 的核心逻辑

Git merge 不是简单比较 `main` 和 `feature-a`。

它会同时看三者：

```text
共同祖先 C0
当前分支 main
要合并的分支 feature-a
```

核心逻辑是：

```text
以共同祖先 C0 为参照：
看 main 从 C0 开始改了什么
看 feature-a 从 C0 开始改了什么
然后尝试把两边改动合起来
```

---

#### 3. 不冲突的情况

共同祖先 `C0`：

```python
x = 1
y = 10
```

`main` 改了 `y`：

```python
x = 1
y = 20
```

`feature-a` 改了 `x`：

```python
x = 100
y = 10
```

Git 会判断：

```text
main 改了 y
feature-a 改了 x
两边改的地方不同
```

所以可以自动合并成：

```python
x = 100
y = 20
```

---

#### 4. 冲突的情况

共同祖先 `C0`：

```python
method = "baseline"
max_iter = 100
```

`main` 改成：

```python
method = "algorithm_B"
max_iter = 100
```

`feature-a` 改成：

```python
method = "algorithm_A"
max_iter = 100
```

两边都改了同一个位置，而且改法不同。

这时 Git 不知道该保留谁，就会产生冲突。

冲突文件中可能显示：

```python
<<<<<<< HEAD
method = "algorithm_B"
=======
method = "algorithm_A"
>>>>>>> feature-a
max_iter = 100
```

这里：

```text
HEAD 部分 = 当前分支 main 的内容
feature-a 部分 = 被合并进来的分支内容
```

---

#### 5. 冲突时应该怎么处理

冲突时，不是固定保留 `main`，也不是固定保留 `feature-a`。

你需要人工决定最终结果。

可以选择保留 main：

```python
method = "algorithm_B"
max_iter = 100
```

也可以选择保留 feature-a：

```python
method = "algorithm_A"
max_iter = 100
```

也可以自己融合成新版本：

```python
method = "algorithm_A_with_B_setting"
max_iter = 100
```

然后执行：

```bash
git add model.py
git commit
```

这个提交就是最终的 merge commit。

---

#### 6. 最核心图

```text
          A1 -- A2   feature-a
         /
C0
         \
          M1 -- M2   main
```

执行：

```bash
git switch main
git merge feature-a
```

意思是：

```text
把 feature-a 从 C0 以来的改动，合并进 main 当前状态，得到
```
```
          A1 -- A2   feature-a
         /        \
C0                 M3   main
         \        /
          M1 -- M2
```
---

### `git restore`

恢复工作区改动：

```bash
git restore demo.txt
```

取消暂存：

```bash
git restore --staged demo.txt
```

如果都不要了：

```bash
git restore --staged demo.txt
git restore demo.txt
```

---

### `git clean`

预览会删除什么：

```bash
git clean -n
```

删除未跟踪文件：

```bash
git clean -f
```

连未跟踪目录一起删除：

```bash
git clean -fd
```

---


### 备注 1 ：`git add` 之后又修改文件怎么办

假设你先执行：

```bash
git add demo.txt
```

然后又继续改 `demo.txt`。

这时会同时有两份版本：

- 暂存区版本：`git add` 那一刻的版本
- 工作区版本：你后来又改过的版本

所以：

**`git commit` 提交的是暂存区版本，不一定是你后来刚改完的最新版本。**

如果想提交最新版本，必须再执行一次：

```bash
git add demo.txt
```

你可以把 `git add` 理解成：

**给当前文件拍一张准备提交的快照。**

---

### 备注 2 ：`to be committed` 和 `not staged for commit`

最简单记法：

- `to be committed` = 已经 `add` 了
- `not staged for commit` = 改了，但还没 `add`

---
###  备注 3 ：reset 和 restore

![git status](https://raw.githubusercontent.com/Sean0330/CS_Fundamental/main/images/git_restore_reset.png)

#### 图中右边三个 `reset`

这三条线都表示：

**把提交往回退。**

区别只在于：  
**回退后，改动保留在哪一层。**

- `git reset --soft HEAD~`  
  撤销 `commit`，**保留暂存区和工作区**  
  可以理解成：**撤销提交，但保留 add**

- `git reset --mixed HEAD~`  
  撤销 `commit`，也撤销 `add`，**但保留工作区修改**  
  可以理解成：**撤销提交，也撤销暂存，但文件改动还在**

- `git reset --hard HEAD~`  
  撤销 `commit`，撤销 `add`，也撤销工作区修改  
  可以理解成：**全部回退，文件也恢复到旧版本**

最短记法：

- `--soft`：退 `commit`，保留 `add`
- `--mixed`：退 `commit` 和 `add`，保留文件
- `--hard`：全退，文件也不要

---

#### 图中左边三个 `restore`

这三条线不是“退提交”，而是：

**恢复文件，或者取消暂存。**

- `git restore --staged <file>`  
  **取消暂存**  
  也就是把文件从暂存区拿下来，但工作区内容还在

- `git restore <file>`  
  **用暂存区版本恢复工作区文件**  
  也就是把工作区文件恢复成 `add` 之后那一版

- `git restore --source=<commit> <file>`  
  **用某次提交里的版本恢复工作区文件**  
  也就是把历史版本中的文件拿出来覆盖当前工作区

最短记法：

- `--staged`：取消 `add`
- `restore <file>`：按暂存区恢复
- `--source=<commit>`：按历史提交恢复

---

### 全流程总结

#### 1. 初始化仓库

```bash
git init
```

#### 2. 创建 `demo.txt`

```bash
echo "This is my first demo file." > demo.txt
echo "Version: 1" >> demo.txt
echo "I am learning Git." >> demo.txt
```

#### 3. 第一次提交

```bash
git add demo.txt
git commit -m "add demo file"
```

#### 4. 再修改并提交一次

```bash
echo "Add one more line for test." >> demo.txt
git add demo.txt
git commit -m "add information for test commit and add"
```

#### 5. 创建并切换到新分支

```bash
git switch -c feature-demo
```

#### 6. 在新分支上修改并提交

```bash
echo "This line is added in feature-demo branch." >> demo.txt
echo "Another line for branch test." >> demo.txt

git add demo.txt
git commit -m "update demo in feature branch"
```

#### 7. 查看分支结构

```bash
git log --oneline --decorate --graph --all
```

#### 8. 切回主分支并合并

```bash
git switch master
git merge feature-demo
```

#### 9. 删除分支

```bash
git branch -d feature-demo
```


## 三. 本机与服务器连接 GitHub

### 3.1 Windows 连接 GitHub

这件事的核心逻辑其实很简单：

#### 3.1.1 本机有一对 key

- 私钥留在你电脑里
- 公钥可以给 GitHub

#### 3.1.2 GitHub 保存你的公钥

以后 GitHub 收到你的 SSH 认证请求时，就拿这个公钥来比对。

#### 3.1.3 本机拿私钥证明身份

GitHub 如果发现能和公钥配对成功，就说明是你这台机器。

#### 3.1.4 配对成功后，就完成绑定

以后就可以用 SSH 方式：
正常流程就是：

1. 先检查本机有没有 SSH key
2. 如果没有，就生成一对 key
3. 把公钥 `.pub` 加到 GitHub
4. 用 `ssh -T git@github.com` 测试
5. 测试成功后，就可以用 SSH 方式管理 GitHub 仓库

最标准的命令顺序是：

```bash
ls -al ~/.ssh
ssh-keygen -t ed25519 -C "你的GitHub邮箱"
cat ~/.ssh/id_ed25519.pub
ssh -T git@github.com
```

其中：

- `id_ed25519`：私钥，自己留着，不能上传
- `id_ed25519.pub`：公钥，要加到 GitHub

GitHub 页面操作是：

**头像 → Settings → SSH and GPG keys → New SSH key**

然后把 `.pub` 的内容粘贴进去。

如果看到：

```bash
Hi 用户名! You've successfully authenticated, but GitHub does not provide shell access.
```

意思是：

- 已经成功认证
- GitHub 不提供 shell，所以这不是报错

这就表示： **Windows 本机已经成功连上 GitHub。**

### 3.2 Linux 服务器连接 GitHub

#### 3.2.1 生成一把 GitHub 专用 SSH key

```bash
ssh-keygen -t ed25519 -C "github-on-linux-server" -f ~/.ssh/id_ed25519_github
```

生成后会得到：

- `~/.ssh/id_ed25519_github`：私钥
- `~/.ssh/id_ed25519_github.pub`：公钥

---

#### 3.2.2 查看公钥并加到 GitHub

```bash
cat ~/.ssh/id_ed25519_github.pub
```

复制输出内容，然后到 GitHub：

**头像 → Settings → SSH and GPG keys → New SSH key**

把公钥粘贴进去保存。

---

#### 3.2.3 配置服务器使用这把 key 连接 GitHub

```bash
nano ~/.ssh/config
```

写入：

```sshconfig
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github
  IdentitiesOnly yes
```

---

#### 3.2.4 设置权限

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/id_ed25519_github
chmod 644 ~/.ssh/id_ed25519_github.pub
```

`chmod` 是用来**修改文件或文件夹权限**的。  
这里的作用是让 SSH 相关文件权限足够安全，否则 SSH 可能拒绝使用这些 key。

最简单理解：

- `700 ~/.ssh`：只有你自己能访问这个 `.ssh` 文件夹
- `600 ~/.ssh/config` 和私钥：只有你自己能读写
- `644 公钥`：自己可读写，别人只能读

---

#### 3.2.5 测试连接

```bash
ssh -T git@github.com
```

如果看到类似：

```bash
Hi 用户名! You've successfully authenticated, but GitHub does not provide shell access.
```

就说明服务器已经成功连上 GitHub。



## 四. 通过 Git 将代码同步到 GitHub

这一部分的核心是：  
**怎么让本地仓库和 GitHub 上的远程仓库保持同步。**

可以先把 GitHub 理解成**远程仓库**，把你电脑或服务器里的项目理解成**本地仓库**。  
所以你平时做的事情，本质上就是两种方向：

- **远程 → 本地**； **本地 → 远程**

对应最核心的命令就是：

- `git clone` `git pull` `git push`

---
才外还包括 `git remote`

### 4.1 `git clone`

`clone` 的意思是：

**第一次把 GitHub 上的整个项目复制到本地。**

例如：
```bash
git clone git@github.com:Sean0330/practice_pytorch.git
```

这个命令不只是“下载文件”，它实际上同时做了这几件事：

1. 把远程仓库完整复制到本地  
2. 自动在本地创建一个 Git 仓库  
3. 自动把远程仓库和本地仓库关联起来  
4. 默认给远程仓库起名为 `origin`

所以要记住：

**`clone` 之后，本地已经是 Git 仓库了，不需要再执行 `git init`。**
所以这时你直接执行：

```bash
git log
git status
git remote -v
```

都是正常可用的。而且你会发现：

**`git log` 看到的不只是你后面自己的提交，连 clone 之前远程仓库原本的历史也都会在。**

---

### 4.2 `git pull`

`pull` 的意思是：

**把远程仓库的最新内容拉到本地。**

也就是：

**远程 → 本地**

例如：

```bash
git pull origin main
```

意思是：

- 从远程仓库 `origin`
- 把 `main` 分支的最新内容
- 拉到你当前本地分支里

最简单理解就是：

**先看看 GitHub 上有没有更新，把它同步到本地。**

---

### 4.3 `git push`

`push` 的意思是：

**把本地已经提交好的内容上传到远程仓库。**

也就是：

**本地 → 远程**

例如：

```bash
git push origin main
```

意思是：

- 把你本地当前<span style="color:red;">分支</span>的提交
- 推到远程仓库 `origin` 的 `main` 分支

最简单理解就是：

**把你本地已经整理好的代码传到 GitHub。**

---

### 4.4 `git remote`

`git remote` 是用来管理：

**本地仓库和远程仓库之间的连接关系。**


#### 4.4.1. 查看远程仓库

```bash
git remote -v
```

作用：

**查看当前本地仓库连接了哪些远程仓库地址。**

常见返回：

```text
origin  git@github.com:Sean0330/practice_pytorch.git (fetch)
origin  git@github.com:Sean0330/practice_pytorch.git (push)
```

意思：

- `origin`：远程仓库名字
- 后面那串：远程仓库地址
- `fetch`：拉取用的地址
- `push`：推送用的地址

---





#### 4.4.2. 添加远程仓库

```bash
git remote add origin git@github.com:Sean0330/practice_pytorch.git
```

作用：

**给本地仓库添加一个远程仓库，并命名为 `origin`。**

最常见的场景是：

**本地已经 `git init` 了，但还没有连接 GitHub。**

---

#### 4.4.3. 删除远程仓库记录

```bash
git remote remove origin
```

作用：

**删除本地仓库里叫 `origin` 的远程仓库记录。**

注意：

这不会删除 GitHub 上真正的仓库，  
也不会删除本地代码，  
只是删除连接关系。

---

#### 4.4.4. 修改远程仓库地址

```bash
git remote set-url origin 新地址
```

作用：

**把 `origin` 原来的远程地址改成新的。**

常见用途：

- 原来是 HTTPS
- 现在想改成 SSH

---

#### 你最该记住的

- `origin` 是远程仓库名字
- `main` 是分支名
- `git remote -v`：看现在连了谁
- `git remote add origin 地址`：把 GitHub 仓库加到本地

---
### 4.5. `git tag`

#### 4.5.1. 语法

##### 给当前提交打标签
```bash
git tag v1.0
```

##### 查看所有标签
```bash
git tag
```

##### 查看某个标签对应的提交信息
```bash
git show v1.0
```

##### 给指定提交打标签
```bash
git tag v1.0 <commitID>
```

##### 删除本地标签
```bash
git tag -d v1.0
```

##### 推送单个标签到 GitHub
```bash
git push origin v1.0
```

##### 推送所有标签到 GitHub
```bash
git push origin --tags
```

---

#### 4.5.2. 应用场景

##### 场景一：刚完成一个重要版本
比如你刚做完第一版稳定版本，这时先提交，再打标签：

```bash
git add .
git commit -m "finish first stable version"
git tag v1.0
```

这表示：

- 先保存一次正式提交
- 再给这次提交贴上 `v1.0` 这个名字

---

##### 场景二：想把这个版本同步到 GitHub
如果你希望 GitHub 上也能看到这个标签，还要继续推送：

```bash
git push origin main
git push origin v1.0
```

这表示：

- `git push origin main`：把代码提交推到 GitHub
- `git push origin v1.0`：把标签也推到 GitHub

---

##### 场景三：回头给旧版本补标签
如果你后来才发现某次旧提交很重要，可以先看提交记录：

```bash
git log --oneline
```

再给那次提交补标签：

```bash
git tag v1.0 a1b2c3d
```

这表示：

给提交 `a1b2c3d` 补一个 `v1.0` 标签。

---

#### 4.5.3. 注意事项

- `tag` 一般是给**重要版本**用的，不是每次提交都要打
- 一般顺序是：**先 commit，再 tag**
- `git push origin main` 推的是代码和分支更新，**不会自动把 tag 一起推上去**
- 如果要让 GitHub 上也有标签，通常要单独执行：

```bash
git push origin v1.0
```

- 最简单理解：
  - `commit`：保存版本
  - `tag`：给某个重要版本起名字

---

#### 4.5.4. 最常用的一套

```bash
git add .
git commit -m "finish first stable version"
git tag v1.0
git push origin main
git push origin v1.0
```

### 4.6 GitHub 上的 merge 是怎么发生的

比如你先把 `feature-a` push 上去：

```bash
git push -u origin feature-a
```

这时 GitHub 上就会有两个分支：

- `main`
- `feature-a`

然后你可以在 GitHub 网页上发起一个 PR：

```text
base: main
compare: feature-a
```

意思是：把 `feature-a` 的内容合并到 `main`。

然后 GitHub 会显示：

- 改了哪些文件
- 增加了哪些 commit
- 有没有冲突
- 能不能 merge

如果你点了 **Merge**，GitHub 就会把 `feature-a` 合并到 `main`。



---
### 4.7 场景一：本地还没有这个项目
#### 4.7.1 clone repo

如果 GitHub 上已经有仓库，而你本地还没有，  
正确做法就是： 

```bash
git clone git@github.com:Sean0330/practice_pytorch.git
```

执行完以后，Git 会在你当前目录下创建一个新文件夹，通常名字和仓库名一样，例如：

```text
practice_pytorch
```

你要注意：

**git clone 之后，真正的 Git 仓库是 clone 下来的那个项目文件夹，不是你当前所在的外层文件夹。**

例如你当前在：

```text
~/Desktop/GitRepo
```

执行：

```bash
git clone git@github.com:Sean0330/practice_pytorch.git
```

结果会变成：

```text
GitRepo/
    practice_pytorch/
```

所以之后你要进入这个项目目录：

```bash
cd practice_pytorch
```

然后再继续操作 Git的其他操作。

---

##### 4.7.2 clone 之后怎么正常工作

进入仓库目录：

```bash
cd practice_pytorch
```

然后开始改文件。  
改完以后，最常见流程是：

```bash
git add .
git commit -m "update training script"
git push origin main
```

这就是“第一次拿项目之后”的正常使用方法。

---

### 4.8 场景二：本地存在内容项目
#### 如果
- 这个项目你已经 clone 过了 或者本地已经是一个 Git 仓库
- 现在只是继续改、继续同步

这种情况下，一般就**不再用 `clone`** 了。  

##### 流程
```bash
cd 项目目录
git pull origin main
git add .
git commit -m "说明"
git push origin main
```

通常开始改代码前，先同步一下远程，这一步很重要，因为你不一定知道 GitHub 上有没有新改动。  
可能是：

- 你自己在别的机器上更新过
- 别人更新过
- GitHub 上已经比你本地更新了

所以比较稳的习惯是：先 pull，同步远程；再 add 和 commit，整理本地；最后 push，上传到 GitHub。

#### 如果，本地有一批文件，想上传到 GitHub 已有仓库的子文件夹

比如：

- GitHub 上已经有一个仓库
- 仓库里已经有一些内容
- 你本地也有一批文件
- 你希望把本地这批文件放到远程仓库里的某个子文件夹，比如 `notes/` 或 `project1/`

这时候，最稳的做法不是直接在你本地原文件夹里 `git init` 然后硬连远程。  
更推荐这样做：

##### 正确思路

先把远程仓库 clone 下来，再把你本地文件复制进去对应子文件夹，最后提交上传。

也就是：

```bash
git clone 仓库地址
cd 仓库目录
mkdir 子文件夹
把本地文件复制进子文件夹
git add .
git commit -m "..."
git push origin main
```
---

### 4.9. 场景三：Git 分支、merge、tag 场景整理

#### 4.9.1. 这个场景到底在解决什么问题

你现在有一个项目，`main` 上是**原始版本**。  
你准备加入算法 A，但又不想把“原始版本”丢掉，因为以后还要拿它做 baseline 比较。

所以这个场景的核心目标是：

1. 用单独分支开发算法 A，避免直接改乱 `main`
2. A 做完后，把它合并回 `main`，让主线以后都基于 A 继续
3. 把“没加 A 的原始版本”单独标记出来，方便以后随时回看和对比

所以最自然的 Git 思路就是：

```text
原始 main
→ 开 feature-a
→ 在 feature-a 上完成 A
→ 给原始版本打 tag: baseline-no-a
→ 把 feature-a merge 回 main
→ 以后从新的 main 再开下一条分支
```

---

#### 4.9.2. 图形化表达

```text
C0  (tag: baseline-no-a)
|\
| C1
| C2  (feature-a)
|/
M1  (main)
 \
  C3  (feature-b)
```

---

#### 4.9.3. 这张图怎么理解

##### `C0`
最初的原始版本，还没有加入算法 A。

##### `baseline-no-a`
打在 `C0` 上的 tag。  
它表示：

```text
这是“没加 A 的 baseline 版本”
```

##### `C1`、`C2`
这是 `feature-a` 分支上的提交。

比如：

- `C1`：开始加入算法 A
- `C2`：算法 A 完成

##### `M1`
这是 merge commit。  
表示 `feature-a` 已经合并回 `main`。

此时的 `main` 不再是老的原始版本，而是：

```text
已经包含算法 A 的新主线
```

##### `C3`
这是从新的 `main` 再开出来的后续开发分支，比如 `feature-b`。  
说明以后再开发新功能，不是从老版本开始，而是从“已经包含 A 的 main”继续。

---

#### 4.9.4. 实操流程和代码

##### 第一步：从 `main` 开出 `feature-a`

```bash
git switch main
git switch -c feature-a
```

含义是：

- 先确保你站在主线 `main`
- 再新建并切换到 `feature-a`

---

##### 第二步：在 `feature-a` 上开发算法 A

```bash
git add .
git commit -m "add algorithm A step 1"

git add .
git commit -m "finish algorithm A"
```

这两次提交就对应图里的： C1; C2


也就是说，算法 A 的开发历史都留在 `feature-a` 这条线上。

---

##### 第三步：回到 `main`，给原始版本打 tag

```bash
git switch main
git tag baseline-no-a
```

因为这时候 `main` 还没有 merge `feature-a`，所以它还停在原始版本 `C0`。

因此这个 tag 会打在：

```text
C0
```

也就是：

```text
C0  (tag: baseline-no-a)
```

---

##### 第四步：把 `feature-a` 合并回 `main`

```bash
git merge --no-ff feature-a -m "merge feature-a into main"
```

这里用 `--no-ff` 的目的，是**明确保留一次 merge 记录**，这样历史图里会清楚出现一个 `M1`。

对应图里就是：

```text
|/
M1  (main)
```

合并以后，`main` 就表示：

```text
已经包含算法 A 的主线版本
```

---

##### 第五步：从新的 `main` 再开下一条分支

```bash
git switch -c feature-b
```

这表示你后面的新开发，比如功能 B，不再从老的 baseline 出发，而是从已经带有 A 的主线继续。

对应图里：

```text
 \
  C3  (feature-b)
```

---

#### 4.9.5. 一整套命令放一起

```bash
git switch main
git switch -c feature-a

# 修改代码，加入算法 A
git add .
git commit -m "add algorithm A step 1"
git add .
git commit -m "finish algorithm A"

git switch main
git tag baseline-no-a
git merge --no-ff feature-a -m "merge feature-a into main"

git switch -c feature-b
```

---

#### 4.9.6. 怎么查看是不是这个结构

你可以用：

```bash
git log --oneline --graph --decorate --all
```

正常会看到类似这种结构：

```text
* C3xxxx (HEAD -> feature-b) start next work
*   M1xxxx (main) merge feature-a into main
|\\
| * C2xxxx (feature-a) finish algorithm A
| * C1xxxx add algorithm A step 1
|/
* C0xxxx (tag: baseline-no-a) original version
```

---

### 4.10 Git 中 branch、commit、指针、merge、push 的简明说明

#### 1. 最核心的概念

##### commit 是什么
`commit` 就是一次保存下来的版本。

可以把它理解成一个版本节点：

```text
C0 -> C1 -> C2
```

表示项目经历了 3 次提交。

---

##### branch 是什么
`branch` 不是文件夹，也不是一份独立拷贝。  
**branch 本质上只是一个名字，贴在某个最新 commit 上。**

例如：

```text
C0 -> C1 -> C2
              ↑
            main
```

意思就是：

- 现在最新版本是 `C2`
- `main` 这个分支名，当前贴在 `C2` 上

这里“贴在”“指着”这个动作，就是别人说的 **指针**。  
你可以把“指针”理解成：

**一个会移动的标签 / 箭头 / 书签**

---

##### HEAD 是什么
`HEAD` 表示：**你现在正站在哪个 branch 上工作**。

例如：

```text
HEAD -> main -> C2
```

意思是：

- 你当前在 `main`
- 下一次 commit，会接在 `C2` 后面
- 然后 `main` 会往前移动

---

#### 2. 一个完整例子

假设现在主线先做到了 `C2`：

```text
C0 -> C1 -> C2
              ↑
            main
```

---

##### 第一步：从 `main` 开 `feature-a`

开分支时，不会复制项目，只是多贴一个标签：

```text
C0 -> C1 -> C2
              ↑
            main
              ↑
         feature-a
```

此时：

- `main` 和 `feature-a` 内容完全一样
- 因为它们都指向同一个 commit：`C2`

---

##### 第二步：在 `feature-a` 上继续开发

你在 `feature-a` 上做了两次提交，得到 `C3`、`C4`：

```text
C0 -> C1 -> C2 -> C3 -> C4
              ↑           ↑
            main      feature-a
```

这时：

- `main` 还停在 `C2`
- `feature-a` 已经走到 `C4`

这就叫分支分开了。

---

##### 第三步：再从 `C2` 开一个 `feature-b`

如果你回到 `main`，再开 `feature-b`，然后提交一次得到 `C5`，正确图应该是：

```text
               C3 -> C4
              /       ↑
C0 -> C1 -> C2      feature-a
            /↑
          C5 main
          ↑
      feature-b       
```

这里：

- `C3` 和 `C5` 都是从 `C2` 分出去的
- 因为 `feature-a` 和 `feature-b` 都是从 `C2` 开的

---

##### 第四步：把 `feature-a` merge 回 `main`

如果你把 `feature-a` 合并回 `main`，会出现一个新的 merge commit，比如 `M1`：

```text
               C3 -> C4
              /         \
C0 -> C1 -> C2 ---------> M1
          /  ↑           ↑
        C5  main        （merge 后 main）
        ↑
    feature-b                 
```

更准确地说，merge 完以后是：

- 原来 `main` 在 `C2`
- `feature-a` 在 `C4`
- 合并后产生新提交 `M1`
- `main` 从 `C2` 移到 `M1`
- `feature-b` 不受影响，仍在 `C5`

---

#### 3. tag 是什么

`tag` 也是一个标签，但它通常**不动**。

比如你想保存“加 A 之前的基线版本”，在这个例子里就是 `C2`，可以打一个 tag：

```text
               C3 -> C4
              /         \
C0 -> C1 -> C2 ---------> M1
         /  ↑           ↑
       C5  baseline     main
       ↑      -no-a   
  feature-b                         
```

意思是：

- `baseline-no-a` 固定标记在 `C2`
- 以后 `main` 会继续往前走
- 但这个 tag 会一直指着“无 A 的基线版本”

所以：

- **branch = 会动的标签**
- **tag = 通常不动的标签**

---

#### 4. 文件和 branch 的关系

**文件不属于 branch。**  
更准确地说：

- 文件在不同 commit 里可以有不同版本
- branch 只是告诉你：**你现在看到的是哪条历史线上的最新版本**

例如同一个 `main.py`：

- 在 `C2` 里是旧版本
- 在 `C4` 里加了算法 A
- 在 `M1` 里是 merge 后版本

所以不是“文件属于哪个 branch”，而是：

**当前 branch 指向哪个 commit，你就看到那个 commit 对应的文件状态。**

---

#### 5. add、commit、push 到底在干什么

##### `git add .`
把当前目录下的改动放进暂存区。  
不是上传 GitHub，也不是自动进 branch 历史。

##### `git commit`
把暂存区内容做成一个新 commit，接到**当前 branch**后面。

##### `git push origin feature-a`
把本地 `feature-a` 这条 branch 上的新 commit，推到远程 GitHub 的 `feature-a`。

所以 GitHub 知道你 push 到哪个 branch，是因为你明确写了：

```bash
git push origin feature-a
```
---

#### 6. 一个完整操作流程

假设你现在想做算法 A，再继续做 B。

```bash
git switch main
git switch -c feature-a

### 修改代码
git add .
git commit -m "add algorithm A step 1"
git add .
git commit -m "finish algorithm A"

git push -u origin feature-a
```

这时 GitHub 上有了 `feature-a`。

然后回到主线，打基线 tag，再 merge：

```bash
git switch main
git tag baseline-no-a
git merge --no-ff feature-a -m "merge feature-a into main"
git push origin main
git push origin --tags
```

然后继续从新的 `main` 开 `feature-b`：

```bash
git switch -c feature-b
git add .
git commit -m "start feature B"
git push -u origin feature-b
```

---

#### 7. 最后只需要记住这几句话

**commit 是版本节点。**  
**branch 是贴在某个最新 commit 上的可移动标签。**  
**HEAD 表示你当前站在哪个 branch 上。**  
**commit 时，当前 branch 会往前移动。**  
**merge 是把另一条分支的成果并进当前分支，并产生新的 merge commit。**  
**push 是把某个 branch 上的提交历史同步到远程 GitHub。**

---

#### 8. 最值得记住的一张图

```text
               C3 -> C4
              /         \
C0 -> C1 -> C2 ---------> M1
            / ↑           ↑
          C5 baseline    main
          ↑  -no-a      
        feature-b             
```

这样读：

- `C2` 是分叉点
- `feature-a` 从 `C2` 走到 `C4`
- `feature-b` 也从 `C2` 走到 `C5`
- `feature-a` 合并回 `main`，形成 `M1`
- `baseline-no-a` 固定标记 `C2`

这张图基本就把 branch 和指针关系讲完了。

## 五. Git在VS Code上的操作


### 5.1 VS Code 里需要单独认识的内容

这些是你在界面里会直接看到、必须认识的。

#### 1. 源代码管理界面
左侧那个分支样子的图标，点进去就是 Git 主界面。以后大部分 Git 操作都从这里进。

#### 2. `Changes`
这里放的是：

**已经改了，但还没有加入暂存区的文件。**

你看到文件在这里，说明它只是改了，还没准备提交。

#### 3. `Staged Changes`
这里放的是：

**已经加入暂存区，准备提交的文件。**

也就是说，这里的文件会进入下一次 commit。

#### 4. 文件状态字母
这个你必须认识，因为 VS Code 会直接显示。

- `M`：文件被修改了
- `D`：文件被删了
- `U`：新文件，还没有被 Git 跟踪

这个部分应该单独记。

#### 5. 文件右边的按钮
最常用的是两个：

- `+`：把文件加入暂存区
- `-`：把文件从暂存区撤回

这个也要单独记，因为你在界面里会一直点它。

#### 6. 提交说明输入框
这是你写 commit message 的地方。  
不写说明，一般没法正常提交。

#### 7. 右上角 `...`
这是更多 Git 操作入口。  
以后常用的有：

- Pull
- Push
- Fetch
- Sync
- Merge
- Branch

这个不用一开始全会，但你要知道它是“更多操作菜单”。

#### 8. 窗口下方的分支名
这里会显示你当前在哪个分支，比如 `main` 或 `dev`。  
点它以后，一般可以：

- 创建新分支
- 切换分支

这个位置非常重要，因为你很多操作都和“当前在哪个分支”有关。

---

### 5.2 在 VS Code 里怎么操作

这个才是你真正每天会用的。

#### 1. 改文件后怎么看 Git 反应
你在编辑器里改完文件并保存后，去源代码管理界面看。  
这时文件一般会出现在 `Changes` 里，并带上 `M` 或 `U` 之类的标记。

这表示：

**Git 已经发现你改了东西。**

#### 2. 想看具体改了什么
直接点那个文件。  
VS Code 会打开对比界面，让你看哪里变了。

所以：

**点文件 = 看改动细节。**

#### 3. 想把文件准备提交
在文件右边点 `+`。

效果是：

文件从 `Changes` 移到 `Staged Changes`

所以：

**点 `+` = add**

#### 4. 想取消暂存
在 `Staged Changes` 里点文件右边的 `-`。

效果是：

文件回到 `Changes`

所以：

**点 `-` = 把 add 撤回。**

#### 5. 想提交
先确保你要提交的文件已经进了 `Staged Changes`。  
然后在上面的输入框写提交说明。  
再点 `Commit`。

所以：

**写说明 + 点 Commit = 本地提交。**

#### 6. 想上传到 GitHub
提交完以后，再点：

- Push

或者右上角 `...` 里面选 Push。

所以：

**Commit 只是本地保存，Push 才是上传 GitHub。**

#### 7. 想先同步 GitHub 上的最新内容
在开始改代码前，先点：

- Pull

这样能先把远程更新拉到本地。

所以：

**开始工作前先 Pull，是个好习惯。**

#### 8. 想新建分支
点窗口下方当前分支名。  
选创建新分支。  
输入名字，例如 `dev`。

所以：

**点分支名 = branch 操作入口。**

#### 9. 想切换分支
还是点下方分支名。  
然后选你要去的分支。

#### 10. 想合并分支
先切到你想接收内容的那个分支。  
然后在源代码管理--更改的右上角 `...` 里找 branch--Merge（中文是分支--合并） 相关操作，再选来源分支。

在 VS Code 里出现冲突时，不要先记按钮名字，先记这个对应关系：
```
当前更改 Current Change = 你现在所在分支的内容
传入更改 Incoming Change = 被 merge 进来的分支内容
```
如图，当有冲突是，合并编辑器会给你返回如下，可以选择接受incoming；接受current；接受组合；或者在下方自行输入你想要的方式，四个选项

![git status](https://raw.githubusercontent.com/Sean0330/CS_Fundamental/main/images/git_merge_vscode.png)

---
