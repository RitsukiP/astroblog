---
title: 在 PC 上部署 YOLO v8 并训练数据集
published: 2025-06-24
description: 本文档将记录在 Windows PC 上本地部署 YOLO v8 系统并建立数据集和训练的所有步骤。
image: ''
tags: [YOLO, Machine Learning, Computer Vision]
category: 笔记
---

本文档将记录在 Windows PC 上本地部署 YOLO v8 系统并建立数据集和训练的所有步骤。

## Part A. 环境部署和架构设计

运行 YOLO v8 需要安装以下几种最基本的依赖（Dependencies）：

- Conda 环境

- PyTorch

- Ultralytics (即 YOLO v8)

需要按照指定的顺序和步骤执行安装操作：

### 1. Conda 环境配置

Conda 环境分为 [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main) 和 [Anaconda](https://www.anaconda.com/docs/getting-started/anaconda/main)，二者的区别是前者在首次安装时只有最必需的 Python 包，而后者对于最基本的数据处理工作可以上手即用并有可视化的 Navigator 工具管理环境，本文将以 Miniconda 作为安装演示。

#### 1.1 安装

可以通过[官网](https://www.anaconda.com/download/success)选择正确的版本（Windows x64）下载安装向导，按步骤进行安装；其中请务必确保 C 盘留有足够的空间，并让 Miniconda 安装至**位于 C 盘的默认目录**，此举可规避许多不必要的麻烦。

也可使用命令行工具进行安装，详情参考[官网安装方法](https://www.anaconda.com/docs/getting-started/miniconda/install#anaconda-website)

#### 1.2  开始使用并创建虚拟环境

在成功安装后，你可以在搜索中找到 Anaconda Prompt 和 Anaconda Powershell Prompt，这表示已经成安装了 conda 环境。启动二者之一，后将会在终端看到运行目录前出现 `(base)` 字样即为正确运行。

```
conda create -n yolov8 python=3.8
```

创建一个名为 `yolov8` 的虚拟环境并指定 Python 版本为 3.8，这将是 YOLO 训练所用的虚拟环境；**一定要指定版本**以防依赖出错。

```
conda activate yolov8
```

执行命令激活该虚拟环境，当命令提示前端由 `(base)` 转化为 `(yolov8)` 即为成功激活环境；**请确保之后的所有操作都在激活此环境后执行！**

#### 1.3 配置国内镜像源（Optional）

配置国内的镜像源将极大提升下载 PyPI 库的速度，以清华大学 Tuna 源为例，其他操作可参考[官方指引](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)，将清华 Tuna 设定为默认的镜像源：

```
python -m pip install --upgrade pip
pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

### 2. PyTorch 安装

**检查运行环境**：在安装 PyTorch 之前需要确认平台的硬件环境，如果使用 Nvidia GPU 进行训练就需要确认 CUDA 版本；

```
nvidia-smi
```

在终端执行以上命令以查看英伟达的显卡信息，表格第一行 CUDA Version 即为 CUDA 版本。

通常安装 PyTorch 都不会安装其最新版本，打开官网[先前版本页面](https://pytorch.org/get-started/previous-versions/)，找到低于或等于当前 CUDA 版本的 PyTorch 版本即可。若无 GPU 则需要选择 CPU Only 的版本进行安装。

安装命令应类似于：

```
pip install torch==2.4.1 torchvision==0.19.1 torchaudio==2.4.1 --index-url https://download.pytorch.org/whl/cu124
```

如果使用 Clash 进行系统代理，应当正确配置以防国际联网时被中断传输，Clash 的端口默认为 7897，在命令后加入 `--proxy http://127.0.0.1:7897` 以将连接交由代理服务接管：

```
pip install torch==2.4.1 torchvision==0.19.1 torchaudio==2.4.1 --index-url https://download.pytorch.org/whl/cu124 --proxy http://127.0.0.1:7897
```

### 3. Ultralytics (YOLO v8) 安装

妥善管理项目结构有助于后期的使用和迁移，选择一个地方用于存放你的 Ultralytics 系统，例如 `E:\`

#### 3.1 项目架构以及安装

安装 Ultralytics 几种方法，而其中使用 Build from source 的方法可以在之后的使用中灵活修改配置文件；

在其 [GitHub 页面](https://github.com/ultralytics/ultralytics)上下载 .zip 压缩包并将其解压至你想要的地方啊，这个文件夹将会成为项目的根目录，解压完成后进入该文件夹并 Build from source

```
cd ultralytics-main
pip install -e .
```

此时已经完成了 Ultralytics 本体的安装，但仍缺少一些依赖。YOLO v8 的需求文件被放置在第一个版本中，打开初代版本即 v8.0.4 的[页面](https://github.com/ultralytics/ultralytics/tree/v8.0.4)并下载 `requirements.txt` 至 `ultralytics-main` 中；

```
pip install -r requirements.txt
```

执行以上命令以安装 `requirements.txt` 中所有要求的依赖，自此完成 Ultralytics 的全部安装。

在根目录下必须要有一个 `datasets` 文件夹用于存放数据集，执行：

```
mkdir datasets
```

#### 3.2 下载模型

使用 YOLO v8 模型需要在 Ultralytics 的文档中找到 [YOLO v8](https://docs.ultralytics.com/zh/models/yolov8/) 并下载所需要的 .pt 预训练模型文件；

**（Optional）** 为方便管理模型文件，可在 `ultralytics` 下创建 `pt` 文件夹用于存放与训练模型文件。

此时你的项目架构应当类似于：

```
/ultralytics-main/
├── datasets/
├── ultralytics/
│   ├── assets
│   ├── cfg
│   ├── engine
│   ├── pt
│   │	└── _model_name.pt
│   └── ...
└── ...
```



## Part B. 构建数据集

假设你已经拿到了图像和标注数据，你需要将其导出为 YOLO 所支援的格式，导出后你应该有和每个图像文件名一一对应的 .txt 标注信息文件；现在可以开始创建一个数据集了。

在 `/ultralytics-main/datasets` 下创建一个自定义名字的文件夹用于存放单一训练项目的数据，以 data1 为例：

```
cd E:\ultralytics-main\datasets
mkdir data1
cd data1
```

在该文件夹中建立 train 和 val 两个文件夹，并分别在其中创建 images 和 labels 文件夹；train 用于存放训练数据，val 用于存放验证数据，而数据应当分别存储为图像（images）和标注文件（lables），**务必确保划分训练集和验证集的时候图像文件和标注文件一一对应。**

此时项目架构应当类似于：

```
/ultralytics-main/
├── datasets/
│   └── data1
│   	│── train
│   	│	├── images
│   	│	└── labels
│   	└── val
│   		├── images
│   		└── labels
├── ultralytics/
│   └── ...
└── ...
```

> **P.S.**
>
> 本文所述的存放结构与官网默认的有所不同，如此设定的逻辑在于按照步骤划分（先训练，后验证）而非按照文件的类型进行划分，因此需要修改描述文件中的 `train` 和 `val` 路径。

## Part C. 训练数据

### 1. 编写描述文件

描述文件是一个 .yaml 的文件置于数据集路径（此时应当为 `/ultralytics-main/datasets/data1/` ）中，对于不同的训练目的，在官方文档中查找对应的代号，找到存于 `/ultralytics-main/ultralytics/cfg/datasets/` 中的范本，改写后存于指定路径下（**切勿直接修改范本**，拷贝至目标路径后再进行改写）。

描述文件中的类别需要严格按照顺序进行填写，相关信息在导出标注信息文件的时候也会一并导出；例如 ISAT 将类别文件存储在导出文件夹中的 `classes.txt` 中。

### 2. 训练命令 / 脚本

YOLO 的训练可以通过指令或者 Python 脚本开始执行。执行命令之时，确保终端工作于根目录下，Python 脚本也应当置于根目录下。

参数设置可以参考[官方文档](https://docs.ultralytics.com/zh/models/yolov8/#yolov8-usage-examples)，示例指令：

```
yolo task=segment mode=train model=./ultralytics/pt/yolov8n-seg.pt data=./datasets/data1/config.yaml workers=1 epochs=50 batch=16
```

使用脚本可以达到完全一样的效果：

```python
from ultralytics import YOLO

# Load model
model = YOLO('ultralytics/pt/yolov8n-seg.pt')
# Train the model
model.train(data='datasets/data1/config.yaml', workers=1, epochs=50, batch=16)
```

自此已经完成了完整的 YOLO v8 训练流程。
