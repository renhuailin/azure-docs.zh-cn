---
title: Azure Percept 开发工具包安装程序概述
description: 了解有关使用开发工具包安装程序加快使用 Azure Percept 进行高级开发的详细信息
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 02292d4559697bc38d47811fe392dc86427515b4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661861"
---
# <a name="dev-tools-pack-installer-overview"></a>开发工具包安装程序概述

开发工具包安装程序是一种停止解决方案，用于安装和配置开发智能边缘解决方案所需的所有工具。 如果你已经安装了下面列出的任何软件包，则开发工具包安装程序将重新安装这些包，以便你的工具与安装程序软件版本保持一致。

## <a name="mandatory-tools-installed"></a>已安装强制工具

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 (Windows) 或 3.5 (Linux) ](https://www.python.org/)
* [Docker 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure 机器学习 SDK 1。1](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)

## <a name="optional-tools-available-for-installation"></a>可供安装的可选工具

* [Nvidia DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (用于开发 Nvidia 加速器解决方案的工具包) 
* [Intel OpenVino 工具包 2020.2](https://docs.openvinotoolkit.org/) (用于为 Intel 加速器开发解决方案的工具包) 
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) 或 1.2.0 (Linux) ](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5。2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA 工具包10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5。1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>已知问题

- 如果 Docker 未在系统上正常运行，则可选的 Caffe 安装可能会失败。 如果要安装 Caffe，请确保在通过开发工具包安装程序尝试 Caffe 安装之前已安装并运行 Docker。 

- 可选的 CUDA 在不兼容的系统上安装失败。 尝试通过开发工具包安装程序 [10.0.130 安装 CUDA 工具包](https://developer.nvidia.com/cuda-toolkit) 之前，请验证系统兼容性。

## <a name="minimum-requirements"></a>最低要求

* Docker 最低要求：

    * Windows：
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64 位：专业版、企业版或教育版 (版本16299或更高版本) 。

             对于 Windows 10 家庭版，请参阅在 Windows Home 上安装 Docker Desktop。
           - 必须启用 hyper-v 和容器 Windows 功能。
           - 在 Windows 10 上成功运行客户端 Hyper-v 需要以下硬件必备组件：

              - 64位处理器，具有 [二级地址转换 (SLAT) ](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 GB 系统 RAM
              - Bios 级别硬件虚拟化支持必须在 BIOS 设置中启用。 有关详细信息，请参阅虚拟化。

        > [!NOTE]
        > Docker 支持 Windows 上的 Docker Desktop，基于 Microsoft 为 Windows 10 操作系统提供的支持生命周期。 有关详细信息，请参阅 [Windows 生命周期事实数据表](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)。

    * Mac：
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        你的 Mac 必须满足以下要求才能成功安装 Docker 桌面：
         
         - **Mac 硬件必须是2010或更高版本的型号**，其中包含 intel 处理器、intel 的硬件支持 (MMU) 虚拟化（包括扩展页表 (EPT) 和无限制模式）。 您可以通过在终端中运行以下命令来查看您的计算机是否具有此支持： ```sysctl kern.hv_support```

        如果 Mac 支持虚拟机监控程序框架，则会打印命令 ```kern.hv_support: 1``` 。

         - **macOS 必须是版本10.14 或更高版本**。 这就是 Mojave、Catalina 或大 .Sur。 建议升级到最新版本的 macOS。

        如果将 macOS 升级到版本10.15 后遇到任何问题，则必须安装最新版本的 Docker Desktop，才能与此版本的 macOS 兼容。

        - 至少 4 GB 的 RAM。
        - 不得安装版本4.3.30 之前的 VirtualBox，因为它与 Docker 桌面不兼容。

        > [!NOTE]
        > Docker 在最新版本的 macOS 中支持 Docker 桌面。 也就是说，当前版本的 macOS 和之前的两个版本。 随着 macOS 的新主要版本公开发布，Docker 将停止支持最早版本，并支持最新版本的 macOS (除了之前的两个版本) 。 Docker Desktop 目前支持 macOS Mojave、macOS Catalina 和 macOS Big .Sur。

## <a name="instructions"></a>说明

1. 下载适用于 [Windows](https://go.microsoft.com/fwlink/?linkid=2132187)、 [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)和 [Mac](https://go.microsoft.com/fwlink/?linkid=2132296)的开发工具包安装程序。

1. 根据你的平台，启动安装程序时存在一些差异。

    1. 对于 Windows：
    
        1. 单击 " **开发工具安装程序** " 以打开安装向导。
        
    1. 对于 Mac：
    
        1. 下载后，将 Dev-Tools-Pack-Installer 文件移动到 "应用程序" 文件夹。
        
        1. 单击 "Dev-Tools-Pack-Installer" 打开安装向导。
        
        1. 如果收到 "无法识别的开发人员" 安全对话框：
        
            1. 中转到 "系统首选项"-> 安全 "& 隐私->" 常规 "，然后单击" Dev-Tools-Pack-Installer "旁边的" 仍打开 "按钮
        
            1. 再次单击 Dock 上的 Electron 图标
        
            1. 单击 "安全" 对话框中的 "打开" 按钮
    
    1. 对于 Linux：
    
        1. 当浏览器提示时，单击 "保存" 以完成安装程序下载
        
        1. 将执行权限添加到 **appimage** 文件方法 1 (命令行) ：
            
            1. 打开 Linux 终端
            
            1. 在终端中键入以下内容，以跳到 "下载" 文件夹
            
                1. cd ~/Downloads/
                
            1. 在终端中键入以下各项，使 AppImage 可执行文件
            
                1. chmod + x 开发工具-AppImage
                
            1. 在终端中键入以下各项以运行安装程序
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. 将执行权限添加到 **appimage** 文件方法 2 (UI) ：
        
            1. 右键单击 "appimage" 文件，然后选择 "属性"
            
            1. 打开权限选项卡
            
            1. 选中 "允许作为程序执行文件" 框
            
            1. 关闭 "属性" 并打开 appimage 文件

1. 在 " **安装开发工具包安装程序** " 页上，单击 " **查看许可证** " 以查看安装程序中包含的每个软件包的许可协议。 如果接受许可协议中的条款，请选中此框，然后单击 " **下一步**"。

    ![安装程序中的 "许可协议" 屏幕。](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/prototyping/article_images/dev_tools_license_agreements.png)

1. 单击 " **隐私声明** " 查看 Microsoft 隐私声明。 如果你同意隐私声明条款，并且想要将诊断数据发送给 Microsoft，请选择 **"是"** ，然后单击 " **下一步**"。 否则，选择 " **否** "，然后单击 " **下一步**"。

    ![安装程序中的 "隐私声明协议" 屏幕。](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/prototyping/article_images/dev_tools_privacy_statement.png)

1. 在 " **配置组件** " 页上，选择要安装的可选工具 (默认情况下将安装) 。

    1. 如果你使用的是 azure Percept 深色的 Azure Percept 音频 SoM，请确保安装 Intel OpenVino 工具包和 Miniconda3。

    1. 单击 " **安装** " 以继续安装。

    ![显示可用软件包的安装程序屏幕。](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/prototyping/article_images/dev_tools_configure_components.png)

1. 在成功安装所有选择的组件后，向导将转到 " **完成安装向导** " 页。 单击 " **完成** " 退出安装程序。

    ![安装程序完成屏幕。](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/prototyping/article_images/dev_tools_finish.png)

## <a name="docker-status-check"></a>Docker 状态检查

如果安装程序通知你验证 Docker Desktop 处于良好运行状态，请参阅以下步骤：

   1. Windows：
   
      1. 展开系统托盘隐藏图标：
      
         1. 如果隐藏，则展开系统托盘隐藏图标：
         
            * ![系统托盘](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/article_images/SystemTray.png)
         
         1. 验证 Docker 桌面图标是否显示 "Docker Desktop 正在运行"：
         
            * ![Docker 状态](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/article_images/DockerStatusRunning.png)
         
         1. 如果看不到系统托盘中列出的上述图标，请从 "开始" 菜单启动 Docker Desktop。
         
         1. 如果 Docker 提示你重新启动，则可以在重新启动完成并且 Docker 处于运行状态后关闭安装程序并重新启动。 应检测到任何成功安装的第三方应用程序，并且不会自动重新安装该应用程序。

## <a name="next-steps"></a>后续步骤

请查看 [高级开发教程 (本地) ](./advanced-development-local.md)，开始学习 AZURE Percept 深色的高级开发。