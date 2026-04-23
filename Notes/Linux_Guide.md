# Linux 服务器深度学习常用命令手册

这份手册只整理**你真正会用到**、并且**在服务器跑深度学习时最常见**的 Linux 内容。  
目标不是把 Linux 讲全，而是让你能直接上手，知道每条命令什么时候用、怎么看返回结果。

---
# 目录

## 1. 先建立一个总认识
### 1.1 Linux、shell、bash 是什么
### 1.2 apt 和 pip 到底有什么区别
### 1.3 sudo 是什么

## 2. Linux 基础
### 2.1 当前目录、父目录、家目录
### 2.2 常见特殊符号
### 2.3 选项的短格式和长格式
### 2.4 文件的三个时间参数

## 3. 常见文件与目录命令
### 3.1 pwd
### 3.2 ls
### 3.3 cd
### 3.4 mkdir 和 rmdir
### 3.5 touch
### 3.6 cp
### 3.7 rm
### 3.8 mv
### 3.9 tar
### 3.10 zip 和 unzip
### 3.11 文本文件的创建、写入、查看与查找
### 3.12 Linux 中的复制和粘贴
### 3.13 Linux 中常见的退出方式

## 4. 服务器资源查看
### 4.1 看内存：free -h
### 4.2 看磁盘：df -h
### 4.3 看 CPU 和整体负载：top
### 4.4 看 GPU：nvidia-smi

## 5. Tmux 学习记录与完整速查
### 5.1 Tmux 是什么
### 5.2 Tmux 的层级结构
### 5.3 最常用的 session 级操作
### 5.4 最常用的 window 级操作
### 5.5 最常用的 pane 级操作
### 5.6 pane 和 window 操作的标准表述
### 5.7 退出 tmux 与关闭对象的区别
### 5.8 Tqdm 在 tmux 中显示的问题

---

# 1. 先建立一个总认识

## 1.1 Linux、shell、bash 是什么

- **Linux**：操作系统
- **shell**：你和 Linux 交流的命令解释器
- **bash**：最常见的一种 shell

你平时在终端里输入的：

```bash
pwd
ls
cd
python
nvidia-smi
```

这些都可以理解成是在 **bash 里输入 shell 命令**。

一句话记忆：

**Linux 是系统，shell 是命令解释器，bash 是最常见的一种 shell。**

---

## 1.2 apt 和 pip 到底有什么区别

这个最关键，因为你后面一定会反复用到。

### apt 是干什么的

`apt` 是 Linux 系统里的**系统级包管理器**。  
它主要负责安装：

- 系统软件
- 系统工具
- 系统层面的依赖

例如：

```bash
sudo apt update
sudo apt install python3-pip
sudo apt install git
sudo apt install htop
```

这些安装的是**整个系统要用的软件**。

### pip 是干什么的

`pip` 是 **Python 的包管理器**。  
它主要负责安装：

- Python 库
- Python 模块
- 供 Python 代码调用的依赖

例如：

```bash
pip install numpy
pip install torch
pip install pandas
```

这些装的是 **Python 环境里的库**，不是系统软件。

### 最重要的区别

#### apt 装的是“系统应用 / 系统工具”
例如：

- git
- vim
- htop
- python3-pip
- cuda toolkit

#### pip 装的是“Python 库”
例如：

- numpy
- torch
- matplotlib
- jupyter
- pandas

### 一句话记忆

**apt 管系统，pip 管 Python。**
## 1.3 sudo 是什么

`sudo` 是 `superuser do` 的缩写，可以理解为：**临时用管理员权限执行当前这一条命令**。在 Linux 中，普通用户通常只能操作自己的文件、运行自己的程序，不能随意修改系统；而加上 `sudo` 后，就可以执行一些系统级操作，例如安装软件、修改系统配置、管理系统服务等。

例如：

```bash
sudo apt update
sudo apt install git
sudo systemctl restart ssh
sudo nano /etc/ssh/sshd_config
```

`root` 是 Linux 里的最高权限用户，几乎拥有系统中的全部权限；而 `sudo` 不是直接永久变成 `root`，而是**只在当前这条命令执行时，临时借用更高权限**。这也是为什么 Linux 通常不建议一直用 `root` 账号做日常操作，因为权限太高，输错命令时风险也更大。

一般来说，下面这些操作通常需要 `sudo`：

- 安装、更新、卸载系统软件
- 修改 `/etc/` 下的系统配置文件
- 管理系统服务，例如 `systemctl restart`
- 修改某些不属于当前用户的文件或目录
- 执行重启、关机、挂载磁盘等系统级操作

而下面这些命令通常**不需要** `sudo`：

```bash
pwd
ls
cd
python
nvidia-smi
cat 自己有权限读取的文件
```

输入 `sudo` 后，系统一般会要求输入**当前用户自己的密码**，这是为了确认你确实有权限临时执行管理员操作。需要注意的是，不是所有用户都能使用 `sudo`；只有被管理员加入相应权限规则的用户，才能成功执行这些命令。

一句话记忆：**普通用户负责做自己的事，`sudo` 用来临时做系统管理员的事。**
---

# 2. Linux 基础

## 2.1 当前目录、父目录、家目录

Linux 里最重要的目录概念有这几个：

- `.` 表示**当前目录**
- `..` 表示**当前目录的上一层目录**，也叫父目录
- `~` 表示**当前登录用户的家目录**

例如：

```bash
cd .
cd ..
cd ~
```

通常情况下：

- root 用户的家目录是：`/root`
- 普通用户的家目录是：`/home/用户名`

所以很多时候，
<span style="color:red;">这\`~` 可以理解为“当前用户的家目录”</span>.
---

## 2.2 常见特殊符号

- `~`：当前登录用户的主目录
- `~用户名`：指定用户的主目录
- `-`：上次所在目录
- `.`：当前目录
- `..`：上级目录

例如：

```bash
cd ~
cd ~statds
cd -
cd .
cd ..
```

---

## 2.3 选项的短格式和长格式

Linux 命令里的选项通常分两种：

- **短格式选项**：例如 `-l`
- **长格式选项**：例如 `--all`

例如：

```bash
ls -l
ls --all
```
<span style="color:red;">
短格式通常是简写，用一个减号。<br>  
长格式通常更完整，用两个减号。
</span>

---

## 2.4 文件的三个时间参数

每个文件主要有 3 个重要时间参数，可以用 `stat` 查看：

```bash
stat 文件名
```

### 访问时间  **Access Time** (atime)

只要文件内容被读取，访问时间就会更新。  例如：

```bash
cat file.txt
```

读取了文件内容，atime 就可能变化。

### 数据修改时间 **Modify Time**(mtime)


当文件内容发生改变时，mtime 会更新。  例如你编辑了文件、增加了内容。

### 状态修改时间 **Change Time** (ctime)


当文件属性或状态发生改变时，ctime 会更新。  
例如：

- 修改权限
- 修改属主
- 修改文件属性

注意：**ctime 不是 creation time，不是创建时间。**  它更准确地表示**状态变化时间**。

---

# 3. 常见文件与目录命令

## 3.1 pwd  <span style="font-size:15px;">作用：显示当前工作目录。</span>

---

## 3.2 ls

含义：

- `ls`：列出文件
- `ls -l`：详细信息
- `ls -a`：包含隐藏文件
- `ls -lh`：文件大小更容易读

`ls -l` 中的 `-l` 表示长格式显示。

---

## 3.3 cd

```bash
cd 文件夹名
```

例如：

```bash
cd data
cd /home/statds
cd ~
cd ..
cd -
```

含义：

- `cd data`：进入 data 文件夹
- `cd /home/statds`：进入绝对路径
- `cd ~`：回到自己的主目录
- `cd ..`：返回上一级
- `cd -`：回到上次所在目录

应用场景：  
进入项目目录、数据目录、模型目录。

---

## 3.4 mkdir 和 rmdir

### mkdir

`mkdir` 是 **make directories** 的缩写，用于创建目录。

```bash
mkdir test
mkdir -p /home/test/demo
```

<span style="color:red;">`-p` 表示递归创建，缺哪层就自动补哪层。</span>

### rmdir

`rmdir` 是 **remove empty directories** 的缩写，用于删除**空目录**。

```bash
rmdir 空目录名
rmdir -p a/b/c
```

`rmdir -p` 也可以递归删除，但前提是这些目录都必须是空的。

---

## 3.5 touch

`touch` 不只是创建空文件，它还可以修改文件的时间参数。

```bash
touch file.txt
```

- 如果文件不存在，会创建一个空文件
- 如果文件已经存在，会更新它的时间参数

---
## 3.6 cp

`cp` 用于复制文件和目录。

```bash
cp 源文件 目标文件
cp a.txt b.txt
cp -r dir1 dir2
```

<span style="color:red;">`-r` 是 `recursive` 的缩写，表示递归复制。也就是说，当复制对象是一个目录时，不只是复制这个目录本身，还会把目录里面所有子文件、子目录以及更深层的内容一起复制过去。</span>

常见用法还有下面这些：

### 1. 复制文件到指定目录

```bash
cp cangls /tmp/
```

表示把文件 `cangls` 复制到 `/tmp/` 目录下。

### 2. 复制文件并改名

```bash
cp cangls /tmp/bols
```

表示把文件 `cangls` 复制到 `/tmp/` 目录下，并改名为 `bols`。

### 3. 复制目录

```bash
cp -r /root/movie/ /tmp/
```

表示把目录 `movie/` 及其里面所有内容一起复制到 `/tmp/` 目录下。  
复制目录时通常要加 `-r`，否则普通 `cp` 主要只用于复制文件。

### 4. 覆盖提示

如果目标位置已经存在同名文件，有些系统会提示是否覆盖，这是因为很多系统把 `cp` 默认设置成了 `cp -i` 的别名，也就是在覆盖前先询问。

例如：

```bash
cp cangls /tmp/
```

如果 `/tmp/` 下已经有同名文件，可能会提示你是否覆盖。


---

## 3.7 rm

`rm` 是删除命令，可以删除文件或目录，通常不会进入回收站。

```bash
rm 文件名
rm -r 目录名
rm -rf 目录名
```

注意：

- 删除文件：`rm 文件名`
- 删除目录：`rm -r 目录名`，把这个目录和里面所有文件、子目录一起删掉。
- 强制删除：`rm -rf 目录名`，递归+强制

`rm -rf` 很危险，因为它通常不会提示确认。

例如：

```bash
rm -rf /test
```

---

## 3.8 mv

`mv` 是 **move** 的缩写，用于<span style="color:red;">移动和重命名</span>。

```bash
mv old_name new_name
mv file.txt /target/path/
mv movie/ /tmp/
```

- 同目录下常常用于**重命名**
- 不同目录之间常常用于**移动**
- 移动目录**不需要** `-r`,表示把目录 movie/ 移动到 /tmp/。

常见选项：

- `-f`：强制覆盖
- `-v`：显示详细移动信息

例如：

```bash
mv -v old_name new_name
mv -f a.txt /target/path/
```

---

## 3.9 tar

`tar` 用于**归档 / 打包**。归档本身不等于压缩，但经常和压缩一起使用。  
也就是说，`tar` 主要负责把多个文件或目录打成一个包，而真正的压缩通常是配合 `gzip` 或 `bzip2` 完成的。

### 1. 打包

```bash
tar -cvf test.tar 文件或目录
```

含义：

- `-c`：创建打包文件
- `-v`：显示过程
- `-f`：指定文件名

---

### 2. 解包到当前目录

```bash
tar -xvf test.tar
```

含义：

- `-x`：解包
- `-v`：显示过程
- `-f`：指定文件名

这会把 `test.tar` 里的内容解压到**当前目录**。  
如果当前目录下已经有同名文件，使用时要注意是否会被覆盖。

---

### 3. 解包到指定目录

```bash
tar -xvf test.tar -C /tmp
```

表示把 `test.tar` 解压到 `/tmp` 目录。  
这里的 `-C` 是**大写**，表示指定解压位置。  
注意：`-C` 后面的目录通常需要事先存在，`tar` 一般不会自动创建该目录。

---

### 4. 查看 tar 包里面有哪些文件

```bash
tar -tvf test.tar
```

这里不是解包，而是**查看 tar 包内容列表**。

含义：

- `-t`：查看包内容
- `-v`：显示详细信息
- `-f`：指定文件名

执行后通常会列出包内每个文件或目录的信息，例如权限、属主、大小、时间和名称。  
这个命令适合在正式解包前先确认包里到底有什么内容。

---

### 5. 常见压缩格式

#### tar.gz

```bash
tar -zcvf test.tar.gz 目录名
tar -zxvf test.tar.gz
```
是 把“目录名”这个目录打包并压缩，生成一个叫 test.tar.gz 的文件

这里的 `-z` 表示配合 `gzip` 压缩。

#### tar.bz2

```bash
tar -jcvf test.tar.bz2 目录名
tar -jxvf test.tar.bz2
```

这里的 `-j` 表示配合 `bzip2` 压缩。

---

### 6. 使用时常见补充

- `tar` 常用于把多个文件或整个目录打成一个包，方便传输、备份或统一管理。
- `tar -tvf` 只查看内容，不会真的解压。
- `tar -xvf` 默认解压到当前目录。
- `tar -xvf 文件名 -C 目录名` 可以把内容解压到指定位置。
- 打包目录时，通常会保留原有目录结构，所以解压后一般也会恢复出原目录。
- `.tar.gz` 通常对应 `-z`，`.tar.bz2` 通常对应 `-j`。
---
## 3.10 zip 和 unzip

`zip` 常用于把一个或多个文件、目录压缩成 `.zip` 文件；`unzip` 用于把 `.zip` 压缩包解压出来。和 `tar` 相比，`zip` 更像是“直接打包并压缩”，在 Linux、Windows、macOS 之间都比较常见，跨平台使用很方便。

### 1. 压缩单个文件

```bash
zip test.zip file1.txt
```

表示把 `file1.txt` 压缩成 `test.zip`。

---

### 2. 压缩多个文件

```bash
zip test.zip file1.txt file2.txt file3.txt
```

表示把多个文件一起压缩到 `test.zip` 中。

例如：

```bash
zip notes.zip a.txt b.txt c.txt
```

表示把 `a.txt`、`b.txt`、`c.txt` 一起压缩到 `notes.zip`。

---

### 3. 压缩整个目录

如果要压缩目录，一般要加 `-r`，表示递归压缩。

```bash
zip -r test.zip dir1
```

表示把目录 `dir1` 及其里面所有文件、子目录一起压缩到 `test.zip`。

例如：

```bash
zip -r project.zip project/
```

表示把整个 `project/` 目录压缩成 `project.zip`。

<span style="color:red;">如果不加 `-r`，普通 `zip` 主要只处理文件，压缩目录时通常不完整或直接报错。</span>

---

### 4. 压缩多个文件和目录

```bash
zip -r backup.zip dir1 file1.txt file2.txt
```

表示把目录 `dir1` 和文件 `file1.txt`、`file2.txt` 一起压缩到 `backup.zip`。

这在备份项目时很常见，比如同时保存：

- 代码目录
- 配置文件
- 日志文件

---

### 5. 解压到当前目录

```bash
unzip test.zip
```

表示把 `test.zip` 解压到**当前目录**。

如果压缩包里原本有目录结构，解压后一般也会恢复对应的目录结构。

---

### 6. 解压到指定目录

```bash
unzip -d /tmp test.zip
```

表示把 `test.zip` 解压到 `/tmp` 目录。

其中：

<span style="color:red;">- `-d`：手动指定解压位置</span>

例如：

```bash
unzip -d /home/statds/data project.zip
```

表示把 `project.zip` 解压到 `/home/statds/data` 下面。

注意：解压目标目录最好事先存在，这样路径更清楚，也更方便管理。

---

### 7. 查看压缩包里有什么内容

<span style="color:red;">有时候你不想马上解压，只想先看看压缩包里有哪些文件,用 `unzip -l` </span>：

```bash
unzip -l test.zip
```

这里的 `-l` 表示列出压缩包内容。

例如：

```bash
unzip -l project.zip
```

这会显示压缩包里有哪些文件、目录以及它们的大致大小。

这个在你不确定压缩包内容时特别有用。

---

### 8. 覆盖问题

解压时，如果目标位置已经有同名文件，有时会提示你是否覆盖。  
这时你需要特别小心，避免把原有文件误覆盖掉。

例如：

```bash
unzip test.zip
```

如果当前目录下本来就有同名文件，系统可能会询问你：

- 是否覆盖
- 是否跳过
- 是否全部覆盖

---

### 9. 实际使用中的常见场景

#### 场景 1：把几个结果文件打包发给别人

```bash
zip results.zip result1.csv result2.csv plot.png
```

适合把实验结果、图表、日志一次性打包。

#### 场景 2：把整个项目目录压缩备份

```bash
zip -r project_backup.zip project/
```

适合在训练前或改代码前做一个快照备份。

#### 场景 3：把别人发来的压缩包解到指定目录

```bash
unzip -d /raid/data dataset.zip
```

适合把数据包解压到专门的数据目录下，而不是直接解在当前目录。

#### 场景 4：先看压缩包内容，再决定要不要解压

```bash
unzip -l dataset.zip
```

适合避免盲目解压，尤其是在大数据包或不熟悉的压缩包上。

---

### 10. 使用时的补充细节

- `zip` 更适合跨平台传文件，尤其是给 Windows 用户时很常见。
- `zip` 压缩目录时通常要加 `-r`。
- `unzip` 默认解压到当前目录。
- `unzip -d 目录 压缩包名` 可以指定解压位置。
- `unzip -l` 可以先看压缩包里有什么。
- `.zip` 文件通常比 `.tar` 更适合直接发给别人使用。

---
## 3.11 文本文件的创建、写入、查看与查找

这一节主要整理最常用的文本文件操作命令，包括：创建文件、写入内容、追加内容、查看内容、查看尾部内容以及查找关键字。

### 1. 常用命令说明

- `touch`：创建空文件
- `echo`：输出一行内容
- `>`：覆盖写入
- `>>`：追加写入
- `cat`：直接显示整个文件内容
- `less`：分页查看文件内容
- `tail`：查看文件末尾内容
- `grep`：查找文件中的关键字

---

### 2. 基本用法

#### 创建一个空的 txt 文件

```bash
touch test.txt
```

表示在当前目录创建一个空的 `test.txt` 文件。

#### 写入一行内容（覆盖原内容）

```bash
echo "这是第一行" > test.txt
```

表示把 `这是第一行` 写入 `test.txt`。  
如果文件原来有内容，会被覆盖掉。

#### 追加一行内容（保留原内容）

```bash
echo "这是第二行" >> test.txt
```

表示把 `这是第二行` 追加到 `test.txt` 末尾。  
原来的内容会保留。

#### 直接查看整个文件内容

```bash
cat test.txt
```

表示把 `test.txt` 的全部内容一次性显示出来。

#### 分页查看文件内容

```bash
less test.txt
```

表示分页查看 `test.txt`。  
如果文件很长，比 `cat` 更适合。

进入 `less` 后常用操作：

- 空格：下一页
- `b`：上一页
- `q`：退出

#### 查看文件最后几行

```bash
tail test.txt
```

默认查看文件最后 10 行。

```bash
tail -n 5 test.txt
```

表示只看最后 5 行。

#### 实时查看文件末尾变化

```bash
tail -f train.log
```

表示持续监控 `train.log` 的最新内容。  
这个命令特别适合看训练日志、报错信息、程序输出。  
退出时按 `Ctrl + C`。

#### 查找关键字

```bash
grep "第一行" test.txt
```

表示在 `test.txt` 中查找包含 `第一行` 的内容。


---

### 3. 写入和追加时的细节补充

#### 1. `echo` 默认会自动换行

```bash
echo "第一行" > test.txt
echo "第二行" >> test.txt
```

执行后，文件内容通常会是：

```text
第一行
第二行
```

这是因为 `echo` 在输出结束时默认会自动加一个换行。

---

#### 2. 如果不想换行，可以用 `-n`

```bash
echo -n "第一行" > test.txt
echo "第二行" >> test.txt
```

这时文件内容可能会变成：

```text
第一行第二行
```

因为第一条命令没有自动换行，所以第二条内容会直接接在后面。

---


#### 3. 如果内容里有空格，最好加引号

```bash
echo training started > train.log
echo "training started" > train.log
```

很多时候两者都能工作，但如果内容较长、带空格、带特殊符号，最好写成带引号的形式，更稳妥：

```bash
echo "[INFO] epoch 1 finished" >> train.log
```

---

#### 4. 如果想精确控制格式，`printf` 比 `echo` 更稳

例如：

```bash
printf "[INFO] training started\n" > train.log
printf "[INFO] epoch 1 finished\n" >> train.log
```

这里的 `\n` 表示换行。  
当你需要更精确地控制换行、制表符或输出格式时，`printf` 往往比 `echo` 更可靠。




---


## 3.12  Linux 中的复制和粘贴

Linux 中的复制粘贴**不是完全固定的一套快捷键**，而是取决于你当前使用的环境，例如：终端、图形界面程序、SSH 工具、tmux 等。

#### 1. 在普通图形界面程序中
如果你用的是图形界面软件，例如浏览器、文件编辑器、VS Code、LibreOffice 等，通常和 Windows 类似：

```text
Ctrl + C   复制
Ctrl + V   粘贴
Ctrl + X   剪切
```

---

#### 2. 在 Linux 终端中
在很多 Linux 终端里，`Ctrl + C` 不是复制，而是**中断当前命令**。  
所以终端里常见的是：

```text
Ctrl + Shift + C   复制
Ctrl + Shift + V   粘贴
```

这是很多终端模拟器中的常见默认方式，例如 GNOME Terminal、Ubuntu Terminal 等。

---

#### 3. `Ctrl + Insert` 和 `Shift + Insert`
你写的这组：

```text
Ctrl + Insert   复制
Shift + Insert  粘贴
```

在**很多终端里确实也能用**，属于比较常见的兼容快捷键。  
但是它**不是所有 Linux 环境都统一支持**，所以可以记住，但不要当成绝对标准。

---

#### 4. 鼠标选中复制、滚轮中键粘贴
在很多 Linux 图形环境里，尤其是传统 X11 环境中，常见规则是：

- **鼠标选中文本**：即可复制
- **单击鼠标中键（滚轮）**：即可粘贴

这个机制和 `Ctrl+C / Ctrl+V` 是两套不同的复制粘贴系统。  
不过要注意，这种方式在不同桌面环境、终端软件、Wayland 环境下支持情况可能不完全一样。

---

#### 5. 最实用的记忆方式

如果你是在 **终端里**：

```text
Ctrl + Shift + C   复制
Ctrl + Shift + V   粘贴
```

如果你是在 **普通图形界面软件里**：

```text
Ctrl + C   复制
Ctrl + V   粘贴
```

如果上面不行，再试：

```text
Ctrl + Insert   复制
Shift + Insert  粘贴
```

---

#### 6. 一句话总结

Linux 中的复制粘贴没有绝对统一的一套规则：  
图形界面程序通常用 `Ctrl + C / Ctrl + V`，终端里常用 `Ctrl + Shift + C / Ctrl + Shift + V`，而 `Ctrl + Insert / Shift + Insert` 以及“鼠标选中即复制、中键粘贴”在很多环境中也能用，但不一定处处通用。

---

## 3.13 Linux 中常见的退出方式

在 Linux 里，“退出”不是一个固定动作，而是取决于你当前处在什么场景。最常见的可以分成下面几类：

### 1. 你在“看内容”
比如你打开了：

```bash
man ls
less file.txt
```

这时候你不是在编辑，也不是在运行程序，只是在浏览内容。  
最常用的退出键是：

```bash
q
```

这里的 `q` 就是 `quit`，表示退出当前查看界面。

---

### 2. 你在“编辑文件”
这时候要看你用的是什么编辑器。

#### 如果用的是 `nano`

常见退出方式是：

```bash
Ctrl + X
```

退出时通常会问你是否保存。

#### 如果用的是 `vim`

`vim` 不是按一个快捷键直接退出，而是先按：

```bash
Esc
```

然后输入不同命令：

```bash
:q
:q!
:wq
```

含义分别是：

- `:q`：退出
- `:q!`：强制退出，不保存
- `:wq`：保存并退出

所以 `vim` 的退出是命令式的，不是单纯一个快捷键。

---

### 3. 你在“跑一个程序”，想立刻让它停下来
比如你执行了一个程序，它一直在运行，或者你输错命令后想马上打断。

这时候最常用的是：

```bash
Ctrl + C
```

它的作用不是“正常退出界面”，而是：

**中断当前正在运行的命令**

这是 Linux 里非常常见的“强行停下来”的方式。

---

### 4. 你在“交互环境里”
比如你进入了：

```bash
python
```

或者某些程序在等待你继续输入。

这时候常用：

```bash
Ctrl + D
```

它通常表示：

**输入结束 / 退出当前交互环境**

例如：

- 退出 Python 交互界面
- 退出某些命令行交互模式

---

### 5. 你在“终端本身”
也就是你现在什么程序都没开，只是在 shell 里，比如 `bash`。

想退出当前终端登录会话，可以用：

```bash
exit
```

或者：

```bash
Ctrl + D
```

---

### 一个粗暴但实用的记忆方法

- `q`：看东西时退出
- `Ctrl + X`：`nano` 退出
- `:q`：`vim` 退出
- `Ctrl + C`：强行打断当前程序
- `Ctrl + D`：退出交互环境或终端
- `exit`：退出当前 shell

---

### 最关键的一点

不要把所有“退出”都理解成同一个意思。Linux 里常见的“退出”其实对应几种不同动作：

- **关闭查看器**，例如 `q`
- **结束编辑器**，例如 `Ctrl + X`、`:q`
- **中断正在运行的程序**，例如 `Ctrl + C`
- **退出交互环境或 shell**，例如 `Ctrl + D`、`exit`


---

# 4. 服务器资源查看

你跑深度学习时，最常看的其实就是：

- CPU
- 内存
- 磁盘
- GPU

下面四个命令最关键：

```bash
free -h
df -h
top
nvidia-smi
```

---

## 4.1 看内存：free -h

### 命令

```bash
free -h
```

### 真实风格示例

下面这个例子是和你服务器风格一致的示例：

```bash
              total        used        free      shared  buff/cache   available
Mem:           252G        2.1G        244G        2.5M        6.0G        247G
Swap:            0B          0B          0B
```

### 这些字段怎么看

#### total
总内存大小。  
这里是 `252G`，表示机器总内存大约 252 GB。

#### used
已经用掉的内存。  
这里是 `2.1G`。

#### free
完全空闲、尚未被使用的内存。  
这里是 `244G`。

#### shared
共享内存，用于某些系统机制和进程共享。

#### buff/cache
缓存和缓冲区占用的内存。  
Linux 会尽量利用空闲内存做缓存，所以这里有值很正常。

#### available
**最关键**。  
表示现在真正还能继续拿来用的大致内存量。  
这里是 `247G`，说明当前内存非常充足。

#### Swap
交换分区。  
这里是 `0B`，说明这台机器没有启用 swap，或者当前没在用。

### 应用场景

#### 场景 1：程序突然卡住
先看是不是内存不够：

```bash
free -h
```

#### 场景 2：读大数据、跑大模型前
先看 `available` 够不够。

#### 场景 3：程序被系统杀掉
有时候就是内存爆了。

---

## 4.2 看磁盘：df -h

### 命令

```bash
df -h
```

### 你服务器上的真实输出示例

下面这个例子直接用你服务器风格的真实输出：

```bash
Filesystem      Size  Used Avail Use% Mounted on
udev            126G     0  126G   0% /dev
tmpfs            26G  2.6M   26G   1% /run
/dev/sda2       1.8T   73G  1.6T   5% /
tmpfs           126G     0  126G   0% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           126G     0  126G   0% /sys/fs/cgroup
/dev/md0        5.2T   27G  5.0T   1% /raid
/dev/sda1       487M  6.1M  480M   2% /boot/efi
tmpfs            26G   24K   26G   1% /run/user/1000
```

### 这些字段怎么看

#### Filesystem
文件系统或者磁盘分区名称。

#### Size
总大小。

#### Used
已使用空间。

#### Avail
可用空间。

#### Use%
使用率。

#### Mounted on
挂载点，也就是这个分区对应到哪个目录。

### 你这份输出最该看哪几行

#### `/dev/sda2       1.8T   73G  1.6T   5% /`
这是系统盘，也就是根目录 `/`。  
说明：

- 总大小 1.8T
- 已用 73G
- 还能用 1.6T
- 使用率只有 5%

这说明系统盘空间很充足。

#### `/dev/md0        5.2T   27G  5.0T   1% /raid`
这是大容量数据盘，挂载在 `/raid`。  
说明：

- 总大小 5.2T
- 已用 27G
- 还能用 5.0T
- 使用率只有 1%

这意味着你的大数据、模型、结果文件很适合放在 `/raid` 下面。

### 应用场景

#### 场景 1：保存模型时报错
先看是不是磁盘满了。

#### 场景 2：下载数据集前
看看盘够不够。

#### 场景 3：训练中间文件很多
日志、checkpoint、缓存文件会越来越大。

---

## 4.3 看 CPU 和整体负载：top

### 命令

```bash
top
```

如果装了 `htop`，会更好看：

```bash
htop
```

### 你服务器上的真实风格示例

下面这个例子和你服务器上的输出格式一致：

```bash
top - 20:30:18 up  5:27,  3 users,  load average: 0.29, 0.19, 0.17
Tasks: 563 total,   1 running, 367 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.3 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 26405547+total, 25562721+free,  2127072 used,  6301184 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 25976662+avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 2117 root      20   0  308092  20908  10188 S   0.3  0.0   0:03.82 fail2ban-server
 2811 root     -51   0       0      0      0 S   0.3  0.0   0:15.19 irq/146-nvidia
    1 root      20   0  226244  10004   6748 S   0.0  0.0   0:05.75 systemd
```

### 顶部这一行怎么看

```bash
top - 20:30:18 up  5:27,  3 users,  load average: 0.29, 0.19, 0.17
```

含义：

- `20:30:18`：当前时间
- `up 5:27`：系统已经运行了 5 小时 27 分钟
- `3 users`：当前有 3 个登录会话
- `load average: 0.29, 0.19, 0.17`：过去 1 分钟、5 分钟、15 分钟的平均负载

### Tasks 这一行怎么看

```bash
Tasks: 563 total,   1 running, 367 sleeping,   0 stopped,   0 zombie
```

含义：

- 总共有 563 个任务
- 当前 1 个在运行
- 367 个在休眠等待
- 没有停止进程
- 没有僵尸进程

### CPU 这一行怎么看

```bash
%Cpu(s):  0.1 us,  0.3 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

重点看这些：

- `us`：用户程序占 CPU
- `sy`：系统内核占 CPU
- `id`：空闲 CPU

这里 `99.6 id` 说明 CPU 基本是空闲的。

### 内存这一行怎么看

```bash
KiB Mem : 26405547+total, 25562721+free,  2127072 used,  6301184 buff/cache
```

这说明：

- 总内存很大
- 当前已用很少
- 空闲很多
- 缓存也占了一部分

### 进程表怎么看

下面这部分：

```bash
PID USER ... %CPU %MEM ... COMMAND
```

重点看：

- `PID`：进程号
- `USER`：谁启动的
- `%CPU`：CPU 占用
- `%MEM`：内存占用
- `COMMAND`：进程名

### 应用场景

#### 场景 1：服务器很卡
看是不是 CPU 满了。

#### 场景 2：数据预处理慢
看是不是 CPU 在忙，而不是 GPU。

#### 场景 3：想知道是不是你程序在跑
看进程表。

---

## 4.4 看 GPU：nvidia-smi

### 命令

```bash
nvidia-smi
```

这是你跑深度学习时最常用的命令之一。

### 你服务器上的真实风格示例

下面这个例子和你服务器上看到的格式基本一样：

```bash
Mon Apr 20 19:57:29 2026
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 520.61.05    Driver Version: 520.61.05    CUDA Version: 11.8     |
|-------------------------------+----------------------+----------------------|
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla V100-DGXS...  On   | 00000000:07:00.0 Off |                    0 |
| N/A   40C    P0    37W / 300W |     44MiB / 32768MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------|
|   1  Tesla V100-DGXS...  On   | 00000000:08:00.0 Off |                    0 |
| N/A   41C    P0    37W / 300W |      5MiB / 32768MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------|
|   2  Tesla V100-DGXS...  On   | 00000000:0E:00.0 Off |                    0 |
| N/A   40C    P0    39W / 300W |      5MiB / 32768MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------|
|   3  Tesla V100-DGXS...  On   | 00000000:0F:00.0 Off |                    0 |
| N/A   41C    P0    39W / 300W |      5MiB / 32768MiB |      0%      Default |
|                               |                      |                  N/A |
+-----------------------------------------------------------------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      2395      G   /usr/lib/xorg/Xorg                 27MiB |
|    0   N/A  N/A      3225      G   /usr/bin/gnome-shell               15MiB |
|    1   N/A  N/A      2395      G   /usr/lib/xorg/Xorg                  4MiB |
|    2   N/A  N/A      2395      G   /usr/lib/xorg/Xorg                  4MiB |
|    3   N/A  N/A      2395      G   /usr/lib/xorg/Xorg                  4MiB |
+-----------------------------------------------------------------------------+
```

### 第一行：时间

```bash
Mon Apr 20 19:57:29 2026
```

这是执行 `nvidia-smi` 命令时的当前系统时间。

### 第二行：驱动和 CUDA 版本

```bash
| NVIDIA-SMI 520.61.05    Driver Version: 520.61.05    CUDA Version: 11.8     |
```

含义：

- `NVIDIA-SMI 520.61.05`：当前显示的 nvidia-smi / 驱动相关版本信息
- `Driver Version: 520.61.05`：显卡驱动版本
- `CUDA Version: 11.8`：当前驱动可兼容的 CUDA 版本信息

注意：  
这里的 CUDA Version 不等于你 Python 环境里 PyTorch 一定用的是这个版本。  
它更准确地说是：**当前驱动兼容的 CUDA 版本信息**。

### GPU 列表中的主要字段

#### GPU
```bash
0 / 1 / 2 / 3
```
表示第几张 GPU。

#### Name
```bash
Tesla V100-DGXS...
```
表示显卡型号。  
你的机器是 Tesla V100。

#### Persistence-M
```bash
On
```
表示持久模式是否开启。  
一般服务器上开着比较正常。

#### Bus-Id
```bash
00000000:07:00.0
```
显卡在 PCIe 总线中的位置。  
普通使用时不用太关心。

#### Disp.A
```bash
Off
```
是否连接显示输出。  
服务器显卡通常是 Off。

#### Volatile Uncorr. ECC
```bash
0
```
ECC 相关的未校正错误计数。  
这里是 0，表示没有看到这类错误。

### 第二行里的状态字段

#### Fan
```bash
N/A
```
表示这里没有提供可读的风扇信息；很多服务器卡都会这样显示。

#### Temp
```bash
40C
```
GPU 温度。  
40°C 表示当前温度不高。

#### Perf
```bash
P0
```
性能状态。  
一般 P0 表示高性能状态。

#### Pwr:Usage/Cap
```bash
37W / 300W
```

表示：

- 当前功耗：37W
- 功耗上限：300W

如果你训练模型时，这个值通常会上升。

#### Memory-Usage
```bash
44MiB / 32768MiB
```

表示：

- 已占用显存：44 MiB
- 总显存：32768 MiB，也就是 32 GB

#### GPU-Util
```bash
0%
```

GPU 当前利用率。  
0% 表示当前基本没在算。

#### Compute M.
```bash
Default
```

计算模式。  
一般默认就是 `Default`。

#### MIG M.
```bash
N/A
```

MIG 是某些新卡上的多实例 GPU 功能。  
V100 没有这个功能，所以通常是 `N/A`。

### Processes 区域怎么理解

这一块显示当前是谁在占用 GPU。

例如：

```bash
|    0   N/A  N/A      2395      G   /usr/lib/xorg/Xorg                 27MiB |
```

字段解释：

- `GPU`：哪一张卡
- `PID`：进程号
- `Type`：进程类型
- `Process name`：进程名字
- `GPU Memory Usage`：占了多少显存

#### Type 常见含义

- `G`：图形界面相关进程
- `C`：计算进程，通常是你训练代码最关心的类型

例如你以后如果跑 PyTorch，很可能会看到：

```bash
python
python3
train.py
```

或者某些 `C` 类型的进程。

### 应用场景

#### 场景 1：确认程序有没有上 GPU
训练开始后马上敲：

```bash
nvidia-smi
```

如果显存涨了、GPU-Util 上去了，说明真的在用 GPU。

#### 场景 2：程序明明跑了但很慢
你看 `nvidia-smi`，如果 GPU-Util 很低，说明可能根本没用上 GPU，或者数据加载卡住了。

#### 场景 3：多用户抢 GPU
你想看哪块卡空闲，挑空的卡跑。

#### 场景 4：显存爆掉
如果报 CUDA out of memory，就看显存是不是占满了。

### 你这台服务器目前说明什么

从你之前的返回看：

- 有 4 张 Tesla V100
- 每张 32 GB 显存
- 当前基本空闲
- CUDA 版本是 11.8
- 驱动正常

这说明：

**这台机器完全可以跑深度学习，而且 GPU 环境本身是正常的。**

----

# 5 Tmux 学习记录与完整速查

## 5.1 Tmux 是什么

Tmux 是 Linux 终端中的一个**终端复用工具**。它的核心作用包括：

- 在一个终端中管理多个任务
- 允许一个会话中包含多个窗口和多个窗格
- 可以暂时离开会话而不终止其中运行的程序
- 适合服务器开发、训练任务、日志监控和长期运行程序

可以把它理解为：

**一个可分层管理的终端工作空间。**

---

## 5.2 Tmux 的层级结构

学习 tmux 时，最重要的是先理解层级，不要一开始就死记快捷键。

Tmux 的层级结构是：

```text
tmux server → session → window → pane
```

### 5.2.1 session

`session` 是会话，是 tmux 中最大的一层。一个 `session` 可以包含多个 `window`。

可以理解为一个独立的工作区。

例如：

- 一个 `session` 用于训练模型
- 一个 `session` 用于数据处理
- 一个 `session` 用于调试代码

### 5.2.2 window

`window` 是窗口，类似标签页。一个 `session` 可以有多个 `window`。

可以理解为一个会话中的不同任务页。

例如：

- `window 0` 跑训练
- `window 1` 看日志
- `window 2` 编辑文件
- `window 3` 做监控

### 5.2.3 pane

`pane` 是窗格，是在一个 `window` 内进一步分屏形成的小区域。一个 `window` 可以被切分成多个 `pane`。

例如：

- 左边一个 `pane` 跑程序
- 右边一个 `pane` 看 `htop`
- 下边一个 `pane` 看日志输出


---

### 5.2.4 最常见参数的含义

####  `-s`

`-s` 表示 `session name`，用于**指定会话名称**。

例如：

```bash
tmux new -s train
```

表示创建一个名为 `train` 的会话。

---

#### `-t`

`-t` 表示 `target`，用于**指定要操作的目标对象**。

目标通常可以是：

- 某个 `session`
- 某个 `window`
- 某个 `pane`

例如：

```bash
tmux attach -t train
tmux kill-session -t train
tmux new-window -t train
```

这里的 `train` 就是目标。

---

#### `-d`

`-d` 表示 `detached`，用于**以后台 / 脱离方式执行**。

例如：

```bash
tmux new -s train -d
```

表示创建名为 `train` 的会话，但不立即进入，而是让它在后台存在。

---

#### `-n`

`-n` 表示 `name`，常用于**给 window 命名**。

例如：

```bash
tmux new-window -t train -n logs
```

表示在 `train` 这个会话里新建一个名为 `logs` 的窗口。

这个参数很有用，因为默认窗口名可能只是数字，不如有名字清楚。

---

## 5.3 最常用的 session 级操作

### 5.3.1 创建新 session

```bash
tmux new-session -s <session-name>
```

缩写：

```bash
tmux new -s <session-name>
```

例如：

```bash
tmux new -s train
```

表示创建一个名为 `train` 的新会话，并立即进入。

---

### 5.3.2 创建新 session 但不进入

```bash
tmux new -s train -d
```

表示：

- 创建 `train` 会话
- 但不立刻进入
- 让它在后台运行

这在脚本中或提前准备工作空间时很有用。

---

### 5.3.3 查看所有 session

```bash
tmux ls
```

---

### 5.3.4 重新连接 session

```bash
tmux attach -t <session-name>
```

缩写：

```bash
tmux at -t <session-name>
```

例如：

```bash
tmux at -t train
```

---

### 5.3.5 切换 session

```bash
tmux switch -t <session-name>
```

这个命令通常用于**已经在 tmux 里面时**切换到另一个会话。

---

### 5.3.6 重命名 session

```bash
tmux rename-session -t <old-name> <new-name>
```

例如：

```bash
tmux rename-session -t train exp1
```

表示把 `train` 改名为 `exp1`。

---

### 5.3.7 杀死 session

```bash
tmux kill-session -t <session-name>
```

例如：

```bash
tmux kill-session -t train
```

表示关闭整个 `train` 会话。

---

### 5.3.8 脱离当前 session

在 tmux 中按：

```text
Ctrl+b d
```

注意：

这不是关闭 `session`，而是**脱离当前会话**。

脱离后：

- tmux 会话仍然存在
- 会话里的程序仍继续运行
- 以后可以重新连接回来

这是 tmux 最重要的价值之一。

---

## 5.4 最常用的 window 级操作

一个 `session` 可以包含多个 `window`。

### 5.4.1 通过快捷键创建新 window

```text
Ctrl+b c
```

这是最常用的方法。

---

### 5.4.2 通过命令创建新 window

```bash
tmux new-window
```

如果你当前已经在某个 tmux 会话中，这句会在当前会话里创建新窗口。

---

### 5.4.3 在指定 session 中创建新 window

```bash
tmux new-window -t train
```

表示在 `train` 这个会话中创建一个新窗口。

---

### 5.4.4 创建新 window 并命名

```bash
tmux new-window -t train -n logs
```

表示：

- 目标会话是 `train`
- 新建一个 `window`
- 窗口名叫 `logs`

这个很适合一开始就规划结构，例如：

- `train`
- `logs`
- `monitor`

---

### 5.4.5 切换到指定 window

```text
Ctrl+b 数字键
```

例如：

```text
Ctrl+b 0
Ctrl+b 1
Ctrl+b 2
```

---

### 5.4.6 切换到上一个 / 下一个 window

```text
Ctrl+b p
Ctrl+b n
```

其中：

- `p = previous`
- `n = next`

---

### 5.4.7 在前后两个 window 之间切换

```text
Ctrl+b l
```

这里的 `l` 是 `last`，表示在最近两个窗口之间来回切换。

---

### 5.4.8 列出所有 window 以供切换

```text
Ctrl+b w
```

---

### 5.4.9 重命名当前 window

快捷键方式：

```text
Ctrl+b ,
```

命令方式：

```bash
tmux rename-window -t train:1 logs
```

这里通常写成：

```text
session:window
```

例如 `train:1` 表示 `train` 会话中的第 1 个窗口。

---

### 5.4.10 修改当前 window 编号顺序

```text
Ctrl+b .
```

作用是重新编号，相当于调整窗口顺序。

---

### 5.4.11 关闭kill当前 window

```text
Ctrl+b &
```
在 tmux 中，对 `window` 通常不是“离开”，而是：

- **切换到别的 window**,用快捷键
- **关闭当前 window**，kill就是
- 或者 **直接脱离整个 session**，用 Ctrl+b d

---

### 5.4.12 tmux 状态栏中的 window 列表怎么看

例如：

```text
[a] 0:bash  1:bash*  2:third-
```

含义如下：

- `[a]`：当前 session 的名字
- `0`、`1`、`2`：window 的编号
- `bash`、`third`：window 的名字
- `*`：表示**当前所在的 window**
- `-`：表示**上一个待过的 window**

所以在这个例子中：

- 当前 session 名叫 `a`
- 一共有 3 个 window
- 当前正在使用的是 `1:bash`
- 上一个待过的是 `2:third`
  
---

## 5.5 最常用的 pane 级操作

一个 `window` 可以切分成多个 `pane`。

### 5.5.1 上下分屏

快捷键：

```text
Ctrl+b "
```

命令方式：

```bash
tmux split-window -v
```

这里 `-v` 表示上下切分。

---

### 5.5.2 左右分屏

快捷键：

```text
Ctrl+b %
```

命令方式：

```bash
tmux split-window -h
```

这里 `-h` 表示左右切分。

---

### 5.5.3 在指定 window 中分屏

例如：

```bash
tmux split-window -h -t train:logs
```

表示在 `train` 会话中的 `logs` 窗口里做左右分屏。

---

### 5.5.4 关闭当前 pane

```text
Ctrl+b x
```

tmux 通常会询问是否确认关闭。

---

### 5.5.5 在 pane 之间移动

```text
Ctrl+b 方向键
```

即：

```text
Ctrl+b ↑
Ctrl+b ↓
Ctrl+b ←
Ctrl+b →
```

---

### 5.5.6 在 pane 之间循环切换

```text
Ctrl+b o
```

---

### 5.5.7 放大当前 pane，再按一次恢复

```text
Ctrl+b z
```

这不是“平铺”，更准确地说是：

**临时将当前 pane 放大到全屏显示，再按一次恢复原布局。**

---

### 5.5.8 将当前 pane 变成独立 window

```text
Ctrl+b !
```

作用：

**把当前 pane 拆出来，放到一个新 `window` 中。**

---

### 5.5.9 调整 pane 大小

常见写法：

```text
Ctrl+b Alt+方向键
```

或者某些配置下支持：

```text
Ctrl+b Ctrl+方向键
```

实际可根据当前 tmux 配置而定。

---

## 5.6 pane 和 window 操作的标准表述

### 5.6.1 pane 操作

- `"`：将当前窗格平分为上下两块
- `%`：将当前窗格平分为左右两块
- `x`：关闭当前窗格
- `!`：将当前窗格置于新窗口
- `Ctrl + 方向键`：以 1 个单元格为单位移动边缘以调整当前窗格大小
- `Alt + 方向键`：以 5 个单元格为单位移动边缘以调整当前窗格大小

### 5.6.2 window 操作

- `c`：创建新窗口
- `&`：关闭当前窗口
- 数字键：切换至指定窗口
- `p`：切换至上一个窗口
- `n`：切换至下一个窗口
- `l`：在前后两个窗口之间互相切换
- `w`：通过窗口列表切换窗口
- `,`：重命名当前窗口，便于识别
- `.`：修改当前窗口编号，相当于窗口重新排序
- `f`：在所有窗口中查找指定文本

---

## 5.7 退出 tmux 与关闭对象的区别

这是最容易混淆的点。

### 5.7.1 `Ctrl+b d`

表示：

**脱离 session，不关闭。**

### 5.7.2 `Ctrl+b x`

表示：

**关闭当前 pane。**

### 5.7.3 `Ctrl+b &`

表示：

**关闭当前 window。**

### 5.7.4 `tmux kill-session -t name`

表示：

**关闭整个 session。**

所以一定要分清楚：

- 脱离
- 关闭 `pane`
- 关闭 `window`
- 杀死 `session`

它们不是一回事。

---



## 5.8 Tqdm 在 tmux 中显示的问题

正常print和警告等特殊输出，都要走这个终端中出现，但是如果在 Python 中使用了类似如下代码：

```python
log_file = open(log_filename, "a")
sys.stdout = log_file
sys.stderr = log_file
```

那么终端中的标准输出和标准错误输出都会被重定向到文件log_file中了。  
这时，`tqdm` 进度条也可能不会正常显示在 tmux 终端里。因为，tqdm 默认很喜欢往 stderr 这条通道写。

### 一个简单做法

可以显式让 `tqdm` 输出到原始终端：

```python
from tqdm import tqdm
import sys

for i in tqdm(range(100), file=sys.__stderr__):
    ...
```

这样 `tqdm` 会尽量显示在终端中，而不是被日志文件吞掉。因为，Python 还偷偷帮你留了一个“原始备份”：sys.\_\_stderr\_\_
这个“原始备份”通常还指向终端。你把进度条指向这个终端，tqdm就能在终端看到了

