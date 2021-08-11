---
title: '教程：训练第一个 Python 机器学习模型 '
titleSuffix: Azure Machine Learning
description: 如何在 Azure 机器学习中训练机器学习模型。 本教程是由三个部分构成的入门教程系列的第 2 部分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/27/2021
ms.custom: devx-track-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q
ms.openlocfilehash: c96936635898f9173b7eb8e60502ea059420cf0b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758867"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-2-of-3"></a>教程：训练你的第一个机器学习模型（第 2 部分，共 3 部分）

本教程介绍如何在 Azure 机器学习中训练机器学习模型。  本教程是由三个部分构成的教程系列的第 2 部分。

 在该系列的[第 1 部分：运行“Hello world!”](tutorial-1st-experiment-hello-world.md)中， 你学习了如何使用控制脚本在云中运行作业。  

在本教程中，你通过提交用于训练机器学习模型的脚本来执行下一步。 此示例将有助于你了解 Azure 机器学习如何在本地调试和远程运行之间轻松实现一致的行为。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建训练脚本。
> * 使用 Conda 定义 Azure 机器学习环境。
> * 创建控制脚本。
> * 了解 Azure 机器学习类（`Environment`、`Run`、`Metrics`）。
> * 提交并运行训练脚本。
> * 在云中查看代码输出。
> * 将指标记录到 Azure 机器学习。
> * 在云中查看指标。

## <a name="prerequisites"></a>先决条件

- 完成本系列的[第 1 部分](tutorial-1st-experiment-hello-world.md)。

## <a name="create-training-scripts"></a>创建训练脚本

首先，在 model.py 文件中定义神经网络体系结构。 所有训练代码（包括 model.py）都将进入 `src` 子目录。

训练代码来自 PyTorch 中的[介绍性示例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)。 请注意，Azure 机器学习概念适用于任何机器学习代码，而不只是 PyTorch。

1. 在 src 子文件夹中创建 model.py。 将以下代码复制到文件中：

    ```python
    import torch.nn as nn
    import torch.nn.functional as F
    class Net(nn.Module):
        def __init__(self):
            super(Net, self).__init__()
            self.conv1 = nn.Conv2d(3, 6, 5)
            self.pool = nn.MaxPool2d(2, 2)
            self.conv2 = nn.Conv2d(6, 16, 5)
            self.fc1 = nn.Linear(16 * 5 * 5, 120)
            self.fc2 = nn.Linear(120, 84)
            self.fc3 = nn.Linear(84, 10)
        def forward(self, x):
            x = self.pool(F.relu(self.conv1(x)))
            x = self.pool(F.relu(self.conv2(x)))
            x = x.view(-1, 16 * 5 * 5)
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x
    ```
1. 在工具栏中，选择“保存”以保存文件。  如果需要，请关闭选项卡。

1. 接下来，还是在 src 子文件夹中定义训练脚本。 此脚本通过使用 PyTorch `torchvision.dataset` API 来下载 CIFAR10 数据集，设置 model.py 中定义的网络，并通过使用标准 SGD 和互熵损失对该数据集进行两个时期的训练。

    在 src 子文件夹中创建 train.py 脚本：

     ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    
    from model import Net
    
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root="../data",
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor(),
    )
    trainloader = torch.utils.data.DataLoader(
        trainset, batch_size=4, shuffle=True, num_workers=2
    )
    
    if __name__ == "__main__":
    
        # define convolutional network
        net = Net()
    
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    
        # train the network
        for epoch in range(2):
    
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
    
                # zero the parameter gradients
                optimizer.zero_grad()
    
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
    
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                    running_loss = 0.0
    
        print("Finished Training")
    ```

1. 现在你拥有以下文件夹结构：

    :::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="目录结构显示 src 子目录中的 train.py":::


## <a name="test-locally"></a><a name="test-local"></a> 本地测试

选择“保存并在终端中运行脚本”，以直接在计算实例上运行 train.py 脚本。

脚本完成后，选择文件文件夹上方的“刷新”。 你将看到名为 get-started/data 的新数据文件夹。展开此文件夹，查看已下载的数据。  

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-with-data.png" alt-text="文件夹的屏幕截图显示通过在本地运行文件创建的新数据文件夹。":::


## <a name="create-the-control-script"></a><a name="create-local"></a> 创建控制脚本

下面的控制脚本和用于提交“Hello world!”的脚本之间的差异 在于你额外添加了几行来设置环境。

在 get-started 文件夹中创建新的 Python 文件，名为 `run-pytorch.py`：

```python
# run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

> [!TIP]
> 如果在创建计算群集时使用了其他名称，请确保也调整代码 `compute_target='cpu-cluster'` 中的名称。

### <a name="understand-the-code-changes"></a>了解代码更改

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure 机器学习提供了[环境](/python/api/azureml-core/azureml.core.environment.environment)概念来表示一个可重现的、进行了版本控制的 Python 环境，以便用来运行试验。 这里使用的是[特选环境](how-to-use-environments.md#use-a-curated-environment)之一。  从本地 Conda 或 pip 环境创建一个环境也很简单。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      将该环境添加到 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)。
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> 将该运行提交到 Azure 机器学习

选择“保存并在终端中运行脚本”来运行 run-pytorch.py 脚本。

>[!NOTE] 
> 首次运行此脚本时，Azure 机器学习会从 PyTorch 环境生成新的 Docker 映像。 完成整个运行可能需要 3 到 4 分钟。 
>
> 可以在 Azure 机器学习工作室中查看 Docker 生成日志。 单击指路向工作室的链接，选择“输出 + 日志”选项卡，然后选择 `20_image_build_log.txt`。
>
> 在将来的运行中将会重复使用此映像，以加快脚本的运行速度。

生成映像之后，请选择 `70_driver_log.txt`，以查看训练脚本的输出。

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> 如果看到错误 `Your total snapshot size exceeds the limit`，则表明数据文件夹位于在 `ScriptRunConfig` 中使用的 `source_directory` 中。
>
> 选择文件夹末尾的“...”，然后选择“移动”，将“数据”移动到 get-started 文件夹   。  

## <a name="log-training-metrics"></a><a name="log"></a> 记录训练指标

你已在 Azure 机器学习中进行了模型训练，可以开始跟踪一些性能指标了。

当前训练脚本将指标输出到终端。 Azure 机器学习提供了一种机制，用于记录具有更多功能的指标。 通过添加几行代码，你可以在工作室中可视化指标并在多个运行之间比较指标。

### <a name="modify-trainpy-to-include-logging"></a>修改 train.py 以包含日志记录

1. 修改 train.py 脚本，以包含另外两行代码：
    
    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    # ADDITIONAL CODE: get run from the current context
    run = Run.get_context()
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root='./data',
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor()
    )
    trainloader = torch.utils.data.DataLoader(
        trainset,
        batch_size=4,
        shuffle=True,
        num_workers=2
    )
    if __name__ == "__main__":
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
        # train the network
        for epoch in range(2):
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
                # zero the parameter gradients
                optimizer.zero_grad()
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    # ADDITIONAL CODE: log loss metric to AML
                    run.log('loss', loss)
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

2. 保存此文件，然后根据需要关闭该选项卡。

#### <a name="understand-the-additional-two-lines-of-code"></a>了解这两行额外添加的代码

在 train.py 中，你通过使用 `Run.get_context()` 方法从训练脚本本身中访问 run 对象，并使用该对象来记录指标：

```python
# ADDITIONAL CODE: get run from the current context
run = Run.get_context()

...
# ADDITIONAL CODE: log loss metric to AML
run.log('loss', loss)
```

Azure 机器学习中的指标具有以下特点：

- 按试验和运行进行组织，因此可以轻松地跟踪和比较指标。
- 配备了一个 UI，使你能够在工作室中可视化训练性能。
- 设计用于进行扩展，因此即使在运行数百个试验的情况下，你也始终有这些优势。

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> 将该运行提交到 Azure 机器学习

选择 run-pytorch.py 脚本的选项卡，然后选择“保存并在终端中运行脚本”，来重新运行 run-pytorch.py 脚本。 

这一次，当你访问工作室时，请转到“指标”选项卡，此时可以看到有关模型训练损失的实时更新！ 训练开始之前可能需要 1 到 2 分钟。  

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="“指标”选项卡中的训练损失图。":::

## <a name="next-steps"></a>后续步骤

在本课程中，你从基本的“Hello world!” 脚本升级成了更逼真的训练脚本，该脚本要求运行特定的 Python 环境。 你已了解如何使用特选的 Azure 机器学习环境。 最后，你看到了如何通过几行代码将指标记录到 Azure 机器学习。

创建 Azure 机器学习环境还有其他方法，包括[从 pip 的 requirements.txt](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) 文件创建，或者[从现有的本地 Conda 环境](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-)创建。

在下一课程中，你将了解如何通过将 CIFAR10 数据集上传到 Azure 来处理 Azure 机器学习中的数据。

> [!div class="nextstepaction"]
> [教程：自带数据](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> 如果你想就此完成本教程系列，不再继续进行下一步，请记得[清理你的资源](tutorial-1st-experiment-bring-data.md#clean-up-resources)。
