---
layout: post
title: ML | PyTorch 
categories: [ML]
tags: [ML, AI, pytorch, PINN, DeepONet]
author: wpsze
date: 2024-12-12 10:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/AAFS6jD.png
banner_img: https://i.imgur.com/AAFS6jD.png
---

# PyTorch

2017 年初由 Facebook 開源的另一套建立在 Torch 之上的深度學習框架 PyTorch 因其語法簡潔優雅、概念直觀和易上手的特性，甫推出便迅速走紅，儼然已成為瓜分深度學習市場的有力競爭者。

PyTorch is an optimized tensor library for deep learning using GPUs and CPUs. Automatic differentiation is done with a tape-based system at both a functional and neural network layer level. This functionality brings a high level of flexibility and speed as a deep learning framework and provides accelerated NumPy-like functionality.

To check if your GPU driver and CUDA/ROCm is enabled and accessible by PyTorch,

```python
import torch

print(torch.__version__)
print("torch.cuda.is_available() = ", torch.cuda.is_available())
print("torch.cuda.device_count() = ", torch.cuda.device_count())
print("torch.cuda.current_device() =", torch.cuda.current_device())
print("torch.cuda.device(0) = ", torch.cuda.device(0))
print("torch.cuda.get_device_name(0) = ", torch.cuda.get_device_name(0))
```

General usage,

```python
# 导⼊包和版本查询
import torch 
import torch.nn as nn 
import torchvision 

print(torch.__version__) 
print(torch.version.cuda) 
print(torch.backends.cudnn.version()) 
print(torch.cuda.get_device_name(0))


# 可复现性在硬件设备（CPU、GPU）不同时，完全的可复现性⽆法保证，即使随机种⼦相同。但是，在同⼀个设备上，应该保证可复现性。具体做法是，在程序开始的时候固定torch的随机种⼦，同时也把numpy的随机种⼦固定。

np.random.seed(0) 
torch.manual_seed(0) 
torch.cuda.manual_seed_all(0) 
torch.backends.cudnn.deterministic = True 
torch.backends.cudnn.benchmark = False
```

# Example

```python
1.导入Pyorch 库：
# 导入PyTorch库，用于构建和训练神经网络
import torch
# 导入神经网络模块，用于定义神经网络结构
import torch.nn as nn
# 导入优化器模块，用于定义模型的优化算法
import torch.optim as optim
# 导入函数al模块，用于使用各种激活函数和损失函数
import torch.nn.functional as F
# 导入数据加载和数据集模块，用于处理和加载训练数据
from torch.utils.data import DataLoader, Dataset
# 导入变换模块，用于对图像数据进行预处理
from torchvision import transforms

2.检查 GPU 支持：
# 根据环境选择合适的计算设备
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

3.创建自定义数据集：
class CustomDataset(Dataset):
    """
    自定义数据集类，继承自torch.utils.data.Dataset。
    用于封装任意数据和其对应的标签，方便在神经网络模型中使用。
    
    参数:
    - data: 数据集，可以是任意形式，如numpy数组或Pandas DataFrame。
    - labels: 数据集的标签，通常是一个一维数组或列表。
    - transform: 可选的数据转换函数，用于在获取数据项时对其进行转换。
    """

    def __init__(self, data, labels, transform=None):
        """
        初始化自定义数据集类。
        """
        self.data = data
        self.labels = labels
        self.transform = transform

    def __len__(self):
        """
        返回数据集的大小。
        
        返回:
        - 数据集的样本数量。
        """
        return len(self.data)

    def __getitem__(self, index):
        """
        根据索引获取数据项。
        
        参数:
        - index: 索引值，用于获取数据集中的特定样本。
        
        返回:
        - x: 数据项。
        - y: 数据项的标签。
        """
        x = self.data[index]
        y = self.labels[index]

        # 如果提供了数据转换函数，则应用该函数对数据项进行转换
        if self.transform:
            x = self.transform(x)

        return x, y

4.使用数据增强：
# 定义一个转换器，用于将图像转换为一系列预处理步骤的组合
transform = transforms.Compose([
    # 随机水平翻转图像，增加数据集的多样性
    transforms.RandomHorizontalFlip(),
    # 随机旋转图像，旋转角度在(-10, 10)之间，以增强模型的泛化能力
    transforms.RandomRotation(10),
    # 将图像转换为Tensor，以便于在PyTorch模型中使用
    transforms.ToTensor(),
])

5.数据加载：
# 创建训练数据集实例
# 参数:
# - train_data: 训练数据输入
# - train_labels: 训练数据标签
# - transform: 数据转换函数
train_dataset = CustomDataset(train_data, train_labels, transform=transform)

# 创建数据加载器
# 参数:
# - train_dataset: 训练数据集实例
# - batch_size: 每个批次的数据大小
# - shuffle: 是否在每个epoch开始时打乱数据顺序
train_loader = DataLoader(train_dataset, batch_size=64, shuffle=True)

6.定义神经网络模型：
class Net(nn.Module):
    """
    定义一个神经网络模型。继承自torch.nn.Module。
    该模型包括一个卷积层和两个全连接层。
    """
    def __init__(self):
        """
        初始化模型并定义各层。
        """
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(1, 32, kernel_size=3)  # 卷积层：输入通道数1，输出通道数32，卷积核大小3x3
        self.fc1 = nn.Linear(32 * 28 * 28, 128)  # 全连接层：输入大小32*28*28，输出大小128
        self.fc2 = nn.Linear(128, 10)  # 全连接层：输入大小128，输出大小10

    def forward(self, x):
        """
        定义模型的前向传播过程。
        :param x: 输入数据，一个张量
        :return: 模型输出，一个张量
        """
        x = F.relu(self.conv1(x))  # 经过卷积层后使用ReLU激活函数
        x = x.view(x.size(0), -1)  # 将张量展平
        x = F.relu(self.fc1(x))  # 经过第一个全连接层后使用ReLU激活函数
        x = self.fc2(x)  # 第二个全连接层的输出，此处不使用激活函数
        return x

7.实例化模型和优化器：
#实例化神经网络模型并转移到指定设备上
model = Net().to(device)  

#初始化优化器，使用Adam算法优化模型参数
#学习率设置为0.001，以适应大多数深度学习模型的训练需求
optimizer = optim.Adam(model.parameters(), lr=0.001)

8.定义损失函数：
# 定义损失函数为交叉熵损失，用于分类任务中
criterion = nn.CrossEntropyLoss()

9.训练模型：
# 迭代训练模型，共进行num_epochs轮
for epoch in range(num_epochs):  
    # 遍历训练数据加载器，获取批次索引、数据和目标标签
    for batch_idx, (data, target) in enumerate(train_loader):  
        # 将数据和目标标签移动到指定设备（如GPU）
        data, target = data.to(device), target.to(device)  

        # 清空梯度，避免累积
        optimizer.zero_grad()  
        # 前向传播，获取模型输出
        output = model(data)  
        # 计算模型输出与目标标签之间的损失
        loss = criterion(output, target)  
        # 反向传播，计算梯度
        loss.backward()  
        # 更新模型权重
        optimizer.step()  

        # 按照日志间隔打印训练信息
        if batch_idx % log_interval == 0:  
            print(f"Train Epoch: {epoch} [{batch_idx * len(data)}/{len(train_loader.dataset)} ({100. * batch_idx / len(train_loader):.0f}%)]\tLoss: {loss.item():.6f}")

10.评估模型：
model.eval()  # 将模型设置为评估模式，以禁用dropout等仅在训练时需要的特性
test_loss = 0  # 初始化测试损失为0
correct = 0  # 初始化正确预测的数量为0
with torch.no_grad():  # 在不需要计算梯度的上下文中进行评估，以减少内存消耗
    for data, target in test_loader:  # 遍历测试数据集
        data, target = data.to(device), target.to(device)  # 将数据和目标移动到指定设备
        output = model(data)  # 前向传播，获取模型的输出
        test_loss += criterion(output, target).item()  # 计算并累加测试损失
pred = output.argmax(dim=1, keepdim=True)  # 获取预测的类别索引
correct += pred.eq(target.view_as(pred)).sum().item()  # 计算并累加正确预测的数量

test_loss /= len(test_loader.dataset)  # 计算平均测试损失
test_accuracy = 100. * correct / len(test_loader.dataset)  # 计算测试准确率

# 打印测试集的平均损失和准确率
print(f'Test set: Average loss: {test_loss:.4f}, Accuracy: {correct}/{len(test_loader.dataset)} ({test_accuracy:.0f}%)')

11.保存和加载模型：
# 保存模型的参数状态
torch.save(model.state_dict(), 'model.pth')  

# 实例化一个新的模型对象
model = Net()  
# 加载保存的模型参数状态到新的模型对象中
model.load_state_dict(torch.load('model.pth'))  
# 将模型移动到指定的设备上，例如CPU或GPU
model.to(device)

12.使用 GPU 加速：
# 将模型移动到指定设备上，以适应不同硬件环境（如GPU或CPU）
model = model.to(device)  

# 将数据移动到指定设备上，确保数据与模型在同一硬件环境
data = data.to(device)  

# 将目标值移动到指定设备上，保证目标与数据、模型一致
target = target.to(device)

13.使用 TensorBoard 进行可视化：
from torch.utils.tensorboard import SummaryWriter
  
# 创建一个SummaryWriter对象，用于将训练过程中的损失和准确率等数据写入TensorBoard日志文件
writer = SummaryWriter()
  
# 遍历训练的每个周期(epoch)
for epoch in range(num_epochs):
    # 进行训练和记录...
    # 记录当前周期的训练损失
    writer.add_scalar('Loss/train', loss.item(), epoch)
    # 记录当前周期的训练准确率
    writer.add_scalar('Accuracy/train', accuracy, epoch)
    # ...
  
# 关闭SummaryWriter对象，确保所有数据被正确写入日志文件
writer.close()

14.调整学习率：
# 初始化学习率调度器，以便在训练过程中按策略调整学习率
scheduler = optim.lr_scheduler.StepLR(optimizer, step_size=10, gamma=0.1)  

# 开始训练过程，迭代指定的轮次
for epoch in range(num_epochs):  
    # 更新学习率调度器，根据当前轮次调整学习率
    scheduler.step()  # 更新学习率  
    # 进行训练...

15.使用预训练模型：
# 导入torchvision中的models模块，用于访问预定义的模型架构和预训练模型
import torchvision.models as models
  
# 初始化一个预训练的ResNet-18模型
# ResNet-18是一个深度卷积神经网络，具有18层，广泛应用于图像分类任务中
# 参数pretrained=True表示使用ImageNet数据集预训练的模型权重
model = models.resnet18(pretrained=True)

16.迁移学习：
# 遍历模型的所有参数
for param in model.parameters():  
    param.requires_grad = False  # 冻结模型参数，防止在训练过程中更新这些参数
  
# 替换最后的全连接层以适应新的类别数
model.fc = nn.Linear(model.fc.in_features, num_classes)
```

# References

1. [Check PyTorch version, CPU and GPU(CUDA) in PyTorch](https://dev.to/hyperkai/check-pytorch-version-cpu-and-gpucuda-in-pytorch-6jk)
2. [Pytorch 高频使用代码集锦](https://mp.weixin.qq.com/s/bcIC27Xo_LAaLUegElzOsg)
3. [包含代码逐行解读！PyTorch真的不难，常见的用法就这么多！](https://mp.weixin.qq.com/s/ydlzsZ3wuZt-0CRHeYy3GQ)