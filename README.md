# 轨道交通信号标志的实时识别系统

<div align="center">
  <img src="./model_comparison_mAP50_first200.png" width="80%"/>
</div>

## 📌 项目亮点

- ✅ **mAP50 0.652** - 在参数量基本相同的情况下，识别精度超过yolov8和yolov11
- ⚡ **32 FPS** - 在Realsense D435i上的实时性能
- 🛠️ **三大创新** - LRSA注意力/AKConv/图像增强流水线
- 📱 **轻量化** - 模型仅8.8MB (FP16量化后)

[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![PyTorch 2.0+](https://img.shields.io/badge/PyTorch-2.0%2B-red.svg)](https://pytorch.org/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## ⭐项目概述
本项目旨在设计一种轻量化的深度神经网络，用于在嵌入式设备上实时识别轨道交通信号标志。我们针对YOLO系列模型进行了优化，引入了局部区域自注意力机制（LRSA）和图像增强技术，以提高小目标和遮挡场景下的检测精度。最终模型部署在Realsense D435i上，实现了摄像头画面的实时检测。

## ⭐主要工作
在本研究中，我们不仅在嵌入式设备中进行了图像处理技术的创新，还通过改进YOLO模型的 backbone 部分和将其部署在实时检测设备上，进一步提升了轨道交通信号标志的自动识别能力。我们的工作可以分为三个关键部分：
### 1. 实时图像处理技术
#### (1) 图像增强
我们借鉴了CVPR2024年提出的《Color Shift Estimation-and-Correction for Image Enhancement》方法，通过校正过曝和欠曝区域的亮度、消除因增亮或变暗带来的细节丢失和颜色伪影，显著提升了图像细节和颜色的表现。该方法结合UNet网络、伪正态特征生成器、颜色偏移估计（COSE）模块以及颜色调制（COMO）模块，有效解决了复杂环境下的视觉问题，为后续信号标志的识别提供了更准确的图像输入。
#### (2) 边缘强化
我们使用了一种基于Canny算子的图像边缘强化算法，用于提升图像中边缘的清晰度。在轨道交通信号标志的自动识别中，清晰的边缘对于提取标志的形状至关重要，边缘强化有助于改善检测精度，尤其是在低对比度或模糊图像的情况下
#### (3) 色彩识别与增强
为提高交通信号的识别能力，特别是红绿灯信号的颜色准确识别，我们基于OpenCV实现了色彩识别和增强技术。这一方法通过对图像中的特定颜色（如红绿灯的红、绿、黄）进行精确识别和增强，能够减少由于环境光照变化、物体遮挡或信号标志颜色偏差带来的误判问题，从而确保识别系统的高准确性和可靠性。

### 2. YOLO模型的改进
针对YOLO模型在小目标和遮挡检测中的不足，我们提出了对其backbone部分的改进。我们引入了局部区域自注意力机制（Local-Region Self-Attention, LRSA），并评估了该机制对YOLOv8n和YOLOv11n模型的影响，特别是在不同位置（如p3和p4）的加入对模型参数量和精度的影响。

#### (1) 模块设计
##### 重叠补丁
模仿HPINet的方法，LRSA采用重叠补丁方式增强特征交互。这一设计突破了传统局部区域划分的限制，允许不同局部区域之间的信息重叠，从而帮助模型捕捉跨越局部边界的特征关系，避免因局部区域划分而丢失关键细节。
##### 投影
输入的特征数据与共享的权重矩阵进行运算，保证在不同局部区域能够一致地提取和处理特征，从而提高了模型的通用性和效率。
##### 多头自注意力（MSA）
通过多头自注意力机制，模型能够从多个角度计算注意力权重，并综合不同视角的信息，提升对局部细节的表达能力，从而增强对复杂场景中目标的检测精度。

#### (2) 性能提升
通过将LRSA与YOLO11C2PSA模块结合，我们强化了模型在小目标和复杂区域中的特征捕捉能力。LRSA的加入显著提升了YOLOv11模型在不同尺度目标的适应性，提高了模型对复杂场景的理解和抗干扰性。在实验中，LRSA加入p3位置时，相较于加入p4位置，mAP50提升了0.03，最终达到了FLOPS = 8.8和mAP50 = 0.652的最优性能。

### 3. 模型部署与实时检测
我们将改进后的YOLO模型部署在Realsense D435i摄像头上，实现了实时图像检测。借助Realsense的深度感知能力和高效的嵌入式处理技术，我们的模型能够在复杂的轨道交通环境中实时识别信号标志，并做出快速反应。

## ⭐模型性能

### 性能对比 (OSDAR23测试集)

| Model | Test Size | AP<sup>test</sup> | AP<sub>50</sub><sup>test</sup> | batch 1 fps | batch 32 avg time |
| :---- | :-------: | :---------------: | :---------------------------: | :---------: | :--------------: |
| [**YOLOv8n**](https://github.com/ultralytics/ultralytics) | 640 | **28.6%** | **38.5%** | 556 *fps* | 1.8 *ms* |
| [**YOLOv11n**](https://github.com/ultralytics/ultralytics) | 640 | **34.7%** | **47.9%** | 400 *fps* | 2.5 *ms* |
| [**Ours**](#) | 640 | **41.9%** | **54.9%** | 417 *fps* | 2.4 *ms* |


## ⭐运行环境

### 硬件环境
- GPU: RTX 4090D (24GB) * 2
- CPU: AMD EPYC 9754 128-Core Processor (18 vCPU)
- 内存: 60GB
- 存储: 系统盘30GB + 数据盘85GB

### 软件环境

- PyTorch==2.3.0
- Python==3.12
- CUDA==12.1

## ⭐运行步骤

<details open>
<summary>安装</summary>


  
首先将本仓库克隆到本地电脑上

```bash
git clone git@github.com:chaizwj/yolov8-tricks.git
```
然后使用pip命令在一个[**Python>=3.8**](https://www.python.org/)环境中安装`ultralytics`包，此环境还需包含[**PyTorch>=1.8**](https://pytorch.org/get-started/locally/)。这也会安装所有必要的[依赖项](https://github.com/ultralytics/ultralytics/blob/main/requirements.txt)。




```bash
pip install ultralytics
```
然后还要先要执行以下命令，根据 requirements.txt文件中需要的第三方库进行安装
```bash
pip install -r requirements.txt
```


</details>

<details open>
<summary>使用</summary>


#### 模型训练

在根目录下找到mytrain.py文件，运行下面这行代码

```python
from ultralytics import YOLO


# 加载 yolov8 模型，根据yaml配置文件。每次改进 yolov8 模型,这里更换对应 配置 yaml 就行 
model = YOLO('ultralytics/cfg/models/v8/yolov8-biformer.yaml')

# 选择预训练权重，这里默认导入了 yolov8s.pt 和 yolov8n.pt
model = YOLO('yolov8s.pt')

# 训练 yolov8 模型
results = model.train(data='VisDrone.yaml')
```

如果你想要使用其他版本的 预训练权重，可以去[预训练权重](https://github.com/ultralytics/assets/releases)中下载。

#### 模型预测

在根目录下找到mypredict.py文件，运行下面这行代码

```python
from ultralytics import YOLO


# 加载 yolov8 模型，根据yaml配置文件。每次改进 yolov8 模型,这里更换对应 配置 yaml 就行 
model = YOLO('ultralytics/cfg/models/v8/yolov8-biformer.yaml')

# 选择预训练权重，这里默认导入了 yolov8s.pt 和 yolov8n.pt
model = YOLO('yolov8s.pt')

# 训练 yolov8 模型
results = model.train(data='VisDrone.yaml')
```
### ⭐数据集

数据集已经提前下载好了，在datasets文件夹下有一个VisDrone，里面划分了训练集，测试集，还有验证集



### ⭐一些新增的地方
#### 热力图
在 Hot-Pic文件夹下的hotPic.py代码文件中，可以根据自己的喜好，选择一种生成方式，有 GradCAM, XGradCAM, EigenCAM, HiResCAM 等方式。下面是生成的热力图，仅供参考

<div align="center">
  

![image](https://github.com/chaizwj/yolov8-tricks/assets/90506129/5ad97a66-cd79-4665-a295-938637bf3f61)


              
![image](https://github.com/chaizwj/yolov8-tricks/assets/90506129/f81eab4c-de25-4660-8d23-259e731dd5b6)



</div>



</details>

#### 自定义的实验结果图

在 Experiment-Pic文件夹下有两个py代码文件，可以根据自己的喜好，选择一种生成方式。下面是生成的结果图，仅供参考

<div align="center">
  

![image](https://github.com/chaizwj/yolov8-tricks/assets/90506129/74d2aa1f-f8c5-4bbf-b38b-428276935a5c)
![image](https://github.com/chaizwj/yolov8-tricks/assets/90506129/641d063a-1c17-4544-8343-083f43d1e79b)




</div>


### ⭐持续更新中...
</details>
