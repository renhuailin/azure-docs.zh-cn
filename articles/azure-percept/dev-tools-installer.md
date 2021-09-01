---
title: 安装 Azure Percept 开发工具
description: 详细了解如何使用开发工具包安装程序通过 Azure Percept 加速高级开发
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f445170d3cd2b2cfeb45a85371409a539368ba22
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225003"
---
# <a name="install-azure-percept-development-tools"></a>安装 Azure Percept 开发工具

开发工具包安装程序是一站式解决方案，可安装和配置开发高级智能边缘解决方案所需的所有工具。

## <a name="mandatory-tools"></a>必需的工具

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 或更高版本](https://www.python.org/)
* [Docker 20.10](https://www.docker.com/)
* [PIP3 21.1](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 2.0](https://www.tensorflow.org/)
* [Azure 机器学习 SDK 1.2](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>可选工具

* [NVIDIA DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk)（用于开发 NVIDIA 加速器解决方案的工具包）
* [Intel OpenVINO 工具包 2021.3](https://docs.openvinotoolkit.org/)（用于开发 Intel 加速器解决方案的工具包）
* [Lobe.ai 0.9](https://lobe.ai/)  
* [Streamlit 0.8](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) 或 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda 4.5](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 7.7](https://chainer.org/)
* [Caffe 1.0](https://caffe.berkeleyvision.org/)
* [CUDA 工具包 11.2](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>已知问题

- 如果 Docker 未正常运行，可选的 Caffe、NVIDIA DeepStream SDK 和 Intel OpenVINO 工具包安装可能会失败。 若要安装这些可选工具，请确保在尝试通过开发工具包安装程序进行安装之前安装并运行 Docker。

- Mac 版本上安装的可选 CUDA 工具包为 10.0.130。 CUDA 工具包 11 不再支持在 macOSity 上开发或运行应用程序。

## <a name="docker-minimum-requirements"></a>Docker 最低要求

### <a name="windows"></a>Windows

- Windows 10 64 位：专业版、企业版或教育版（版本 16299 或更高版本）。

- 必须启用 Hyper-V 和容器 Windows 功能。 要在 Windows 10 上成功运行 Hyper-V，需要满足以下硬件先决条件：

    - 具有[二级地址转换 (SLAT) ](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)的 64 位处理器
    - 4 GB 系统 RAM
    - 必须在 BIOS 设置中启用 BIOS 级别硬件虚拟化支持。 有关详细信息，请参阅“虚拟化”。

> [!NOTE]
> Docker 基于 Microsoft 对 Windows 10 操作系统的支持生命周期，在 Windows 上支持 Docker Desktop。 有关详细信息，请参阅 [Windows 生命周期简报](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)。

详细了解如何[在 Windows 上安装 Docker Desktop](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows)。

### <a name="mac"></a>Mac

- Mac 必须是 2010 或更新的型号，并具有以下属性：
    - Intel 处理器
    - Intel 的硬件支持内存管理单元 (MMU) 虚拟化，包括扩展页表 (EPT) 和无限制模式。 可以通过在终端中运行以下命令来检查计算机是否具有此支持：```sysctl kern.hv_support```。 如果 Mac 支持虚拟机监控程序框架，则命令会打印 ```kern.hv_support: 1```。

- macOS 10.14 版或更高版本（Mojave、Catalina 或 Big Sur）。 建议升级到 macOS 的最新版本。 如果将 macOS 升级到版本 10.15 后遇到任何问题，则必须安装最新版本的 Docker Desktop 才能与此版本的 macOS 兼容。

- 至少 4 GB 的 RAM。

- 不得安装版本 4.3.30 之前的 VirtualBox - 它与 Docker Desktop 不兼容。

- Apple M1 上不支持该安装程序。

详细了解如何[在 Mac 上安装 Docker Desktop](https://docs.docker.com/docker-for-mac/install/#system-requirements)。

## <a name="launch-the-installer"></a>启动安装程序

下载适用于 [Windows](https://go.microsoft.com/fwlink/?linkid=2132187)、[Linux](https://go.microsoft.com/fwlink/?linkid=2132186) 或 [Mac](https://go.microsoft.com/fwlink/?linkid=2132296) 的开发工具包安装程序。 根据你的平台启动安装程序，如下所述。

### <a name="windows"></a>Windows

1. 单击“Dev-Tools-Pack-Installer”以打开安装向导。

### <a name="mac"></a>Mac

1. 下载后，将 Dev-Tools-Pack-Installer.app 文件移动到“Applications”文件夹 。

1. 单击“Dev-Tools-Pack-Installer.app”以打开安装向导。

1. 如果出现“无法识别的开发人员”安全对话框，请执行以下操作：

    1. 转到“系统首选项” -> “安全和隐私” -> “常规”，然后单击“Dev-Tools-Pack-Installer.app”旁边的“仍要打开”    。
    1. 单击电子图标。
    1. 在安全对话框中单击“打开”。

### <a name="linux"></a>Linux

1. 当浏览器中出现提示时，单击“保存”以完成安装程序下载。

1. 将执行权限添加到 .appimage 文件：

    1. 打开 Linux 终端。

    1. 在终端中输入以下内容以转到“Downloads”文件夹：

        ```bash
        cd ~/Downloads/
        ```

    1. 使 AppImage 可执行：

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. 运行安装程序：

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. 将执行权限添加到 .appimage 文件：

    1. 右键单击 .appimage 文件并选择“属性”。
    1. 打开“权限”选项卡。
    1. 选中“允许将文件作为程序执行”旁边的框。
    1. 关闭“属性”并打开 .appimage 文件 。

## <a name="run-the-installer"></a>运行外接程序

1. 在“安装开发工具包安装程序”页上，单击“查看许可证”以查看安装程序中包含的每个软件包的许可协议 。 如果接受许可协议中的条款，请选中此框，然后单击“下一步”。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="安装程序中的许可协议屏幕。":::

1. 单击“隐私声明”以查看 Microsoft 隐私声明。 如果同意隐私声明条款，并且想要将诊断数据发送给 Microsoft，请选择“是”，然后单击“下一步” 。 否则，选择“否”，然后单击“下一步” 。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="安装程序中的隐私声明协议屏幕。":::

1. 在“配置组件”页上，选择要安装的可选工具（默认情况下将安装必需工具）。

    1. 如果使用的是 Azure Percept Audio SoM（属于 Azure Percept DK），请确保安装 Intel OpenVino 工具包和 Miniconda3。

    1. 单击“安装”以继续安装。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="显示可用软件包的安装程序屏幕。":::

1. 成功安装所有选定的组件后，向导将转到“完成安装向导”页面。 单击“完成”退出安装程序。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="“安装程序完成”屏幕。":::

## <a name="docker-status-check"></a>Docker 状态检查

如果安装程序通知你验证 Docker Desktop 是否处于良好运行状态，请参阅以下步骤：

### <a name="windows"></a>Windows

1. 展开系统托盘隐藏的图标。

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="系统托盘。":::

1. 验证 Docker Desktop 图标是否显示“Docker Desktop 正在运行”。

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker 状态。":::

1. 如果没有在系统托盘中看到以上图标，请从“开始”菜单启动 Docker Desktop。

1. 如果 Docker 提示重新启动，可以关闭安装程序，在重新启动完成且 Docker 处于运行状态后重启。 任何成功安装的第三方应用程序都应该能被检测到，并且不会自动重新安装。

## <a name="next-steps"></a>后续步骤

请查看 [Azure Percept 高级开发存储库](https://github.com/microsoft/azure-percept-advanced-development)，开始使用适用于 Azure Percept DK 的高级开发。
