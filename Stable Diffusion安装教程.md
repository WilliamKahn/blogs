# Stable Diffusion安装教程



## 1.环境确认

需要使用 N 卡（NVIDIA 显卡），显卡的显存至少需要 4GB 以上显存才能在本地运行。最低配置需要 4GB 显存，基本配置 6GB 显存，推荐配置 12GB 显存或者以上。 

按下 <kbd>Win</kbd>+<kbd>R</kbd>打开输入"cmd"打开控制台

![image-20230506161757104](../../img/image-20230506161757104.png)

在命令行中输入python -V确保自己环境配置的python版本在3.10以上（包含3.10）

如果显示‘python’不是内部或外部命令，表示你电脑中并未安装python

网上安装python的教程比较多可以自行安装（一定要安装3.10以上版本）

```bash
python -V
```

![image-20230506161822246](../../img/image-20230506161822246.png)

确保安装了git工具

```bash
git -v
```

![image-20230506161842551](../../img/image-20230506161842551.png)

## 2.下载Stable Diffusion项目文件

以下提供两种方式下载项目到本地

### 会使用git工具 

选择文件直接克隆项目到本地

```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```

期间如果出现任何443或者time out等问题主要是因为网络问题

多试几次就能克隆下来



### git一直报错或者不会使用git工具

打开[stable-diffusion](https://github.com/AUTOMATIC1111/stable-diffusion-webui.git)

![image-20230506161854908](../../img/image-20230506161854908.png)

解压到自己创建的目录

![image-20230506161907416](../../img/image-20230506161907416.png)



## 3.安装stable diffusion所需的依赖

打开stable-diffusion-webui-master文件

在文件路径中输入cmd然后回车进入到命令行

![image-20230506161914487](../../img/image-20230506161914487.png)

### 配置虚拟环境（非必须步骤可以跳过）

首先安装虚拟环境库

```bash
pip install virtualenv
```

创建虚拟环境

```bash
virtualenv venv
```

激活虚拟环境

```bash
venv\Scripts\activate
```



### 安装依赖

```
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

按道理来说这步结束后就完成了

命令行输入webui-user.bat或者双击该文件

```bash
webui-user.bat
```

但是默认安装的torch文件是cpu版本

我们需要安装gpu版本

首先卸载原先的torch

```bash
pip uninstall torch
```

再安装gpu版本

```
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu117
```

一切pip install 碰到的问题基本都是网络引起的

多试几次肯定能安装上

## 启动
