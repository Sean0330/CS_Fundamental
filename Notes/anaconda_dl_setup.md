## Anaconda 深度学习环境安装完整流程总结

下面是从 **安装 Anaconda** 到 **创建深度学习环境 `dl`**、再到 **导出 `dl_environment.yml`** 的完整流程。

---

### 1. 确认服务器架构

先确认服务器是不是 `x86_64`：

```bash
uname -m
```

如果返回：

```bash
x86_64
```

说明应该下载 Linux x86_64 版本的 Anaconda。

---

### 2. 下载 Anaconda 安装包

建议先回到家目录再下载：

```bash
cd ~
wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
```

---

### 3. 运行安装器

```bash
bash Anaconda3-2024.02-1-Linux-x86_64.sh
```

安装时一般按下面操作：

- 按 `Enter` 继续
- 阅读协议时按空格翻页
- 输入 `yes` 同意协议
- 安装路径默认一般是：

```bash
/home/statds/anaconda3
```

- 如果问你是否初始化 conda，输入：

```bash
yes
```

---

### 4. 如果提示 `/home/statds/anaconda3` 已经存在

先检查是不是已经装好了：

```bash
~/anaconda3/bin/conda --version
```

如果能返回版本号，例如：

```bash
conda 24.1.2
```

说明 Anaconda 其实已经装好了，只是当前终端还不会直接识别 `conda`。

---

### 5. `conda --version` 不行时怎么修复

如果你直接输入：

```bash
conda --version
```

报：

```bash
command not found
```

但下面这句能运行：

```bash
~/anaconda3/bin/conda --version
```

说明问题不是没装，而是 **bash 还没有初始化 conda**。

这时要执行：

```bash
~/anaconda3/bin/conda init bash
source ~/.bashrc
```

然后再试：

```bash
conda --version
```

如果能正常显示版本号，就说明修好了。

> 这里最关键的两句是：
>
> ```bash
> ~/anaconda3/bin/conda init bash
> source ~/.bashrc
> ```
>
> 第一条是让 conda 自动写入 bash 配置，第二条是立刻重新加载配置。

---

### 6. 查看当前有哪些环境

```bash
conda info --envs
```

通常会先看到一个默认环境：

```bash
base
```

---

### 7. 创建自己的深度学习环境

不要什么都装在 `base` 里，建议单独创建一个环境，例如 `dl`：

```bash
conda create -n dl python=3.10
```

如果提示是否继续，输入：

```bash
y
```

---

### 8. 激活环境

```bash
conda activate dl
```

激活成功后，终端前面通常会出现：

```bash
(dl)
```

这表示你已经进入 `dl` 环境了。

---

### 9. 检查当前 Python 和 pip 是否来自 `dl`

```bash
which python
which pip
python --version
```

正常的话应该类似：

```bash
/home/statds/anaconda3/envs/dl/bin/python
/home/statds/anaconda3/envs/dl/bin/pip
Python 3.10.18
```

这说明你现在确实在用 `dl` 环境。

---

### 10. 升级 pip

```bash
pip install --upgrade pip
```

---

### 11. 安装基础库

```bash
pip install numpy pandas matplotlib jupyter
```

注意：`matplotlib` 不要拼错成 `mathplotlib`。

---

### 12. 安装 PyTorch

一开始如果直接装：

```bash
pip install torch torchvision torchaudio
```

可能会装到不适合当前驱动的 CUDA 版本。

你当时装到的是：

```bash
torch 2.6.0+cu124
```

但服务器驱动较老，所以出现了：

- `torch.cuda.is_available()` 返回 `False`
- 报错提示驱动太老，不支持当前 CUDA 版本

---

### 13. 解决 PyTorch 和驱动不匹配的问题

先卸载原来的 PyTorch：

```bash
pip uninstall -y torch torchvision torchaudio
pip cache purge
```

然后安装适合当前驱动的 **cu118** 版本：

```bash
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu118
```

---

### 14. 验证 PyTorch 和 GPU 是否正常

```bash
python -c "import torch; print(torch.__version__)"
python -c "import torch; print(torch.cuda.is_available())"
python -c "import torch; print(torch.cuda.device_count())"
python -c "import torch; print(torch.cuda.get_device_name(0))"
```

如果正常，第二句应该返回：

```bash
True
```

这说明 `dl` 环境里的 PyTorch 已经能正常识别 GPU。

---

### 15. 以后跑代码的标准流程

每次新开终端后，先激活环境：

```bash
conda activate dl
```

然后再运行代码：

```bash
python train.py
```

如果不用了，退出环境：

```bash
conda deactivate
```

---

### 16. 最重要的一步：导出环境文件 `dl_environment.yml`

这个文件非常关键，因为它相当于你当前环境的“清单”或“备份”。

执行：

```bash
conda env export > ~/dl_environment.yml
```

这样会把当前 `dl` 环境的配置信息导出到家目录：

```bash
/home/statds/dl_environment.yml
```

#### 为什么这个文件重要

因为以后如果出现下面这些情况：

- 环境坏了
- 想重装环境
- 换了一台服务器
- 想把环境复制给别人
- 想保留当前环境配置

都可以直接用它重建环境。

#### 查看这个文件

```bash
ls ~/dl_environment.yml
cat ~/dl_environment.yml
less ~/dl_environment.yml
```

#### 以后如何用它重建环境

```bash
conda env create -f ~/dl_environment.yml
```

所以：

> **`dl_environment.yml` 一定要保留好。**  
> 它是你整个深度学习环境最重要的备份文件之一。

---

### 17. 整体顺序总结

正确顺序是：

1. 确认服务器架构
2. 下载并安装 Anaconda
3. 如果 `conda --version` 不行，用：
   ```bash
   ~/anaconda3/bin/conda init bash
   source ~/.bashrc
   ```
4. 创建 `dl` 环境
5. 激活 `dl`
6. 在 `dl` 里安装基础库
7. 在 `dl` 里安装适合驱动的 PyTorch
8. 验证 GPU 是否可用
9. 运行代码
10. 导出并保存 `dl_environment.yml`

---

### 18. 最值得记住的几条命令

```bash
~/anaconda3/bin/conda init bash
source ~/.bashrc
conda create -n dl python=3.10
conda activate dl
pip install numpy pandas matplotlib jupyter
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu118
python -c "import torch; print(torch.cuda.is_available())"
conda env export > ~/dl_environment.yml
```

---

### 19. 一句话总结

整个流程的核心就是：**先把 Anaconda 装好并让 bash 认识 `conda`，再创建并激活 `dl` 环境，在里面安装合适版本的 PyTorch，最后一定导出并保存 `dl_environment.yml`，这样你的环境才算真正搭好并可复现。**
