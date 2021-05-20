---
title: 在 Windows 虚拟桌面中安装 Microsoft Office FSLogix 应用程序容器 - Azure
description: 如何使用应用规则编辑器在 Windows 虚拟桌面中使用 Office 创建 FSLogix 应用程序容器。
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446802"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>使用 FSLogix 应用程序容器安装 Microsoft Office

可以使用 FSLogix 应用程序容器作为模板用于主机池中的其他虚拟机 (VM)，从而快速高效地安装 Microsoft Office。

以下是使用 FSLogix 应用容器有助于更快地进行安装的原因：

- 若要将 Office 应用卸载到应用容器，可减少对 C 驱动器大小的要求。
- VM 的快照或备份会占用较少资源。
- 通过更新单个映像来实现自动化管道可以更轻松地更新 VM。
- 只需一个映像即可将 Office（和其他应用）安装到 Windows 虚拟桌面部署中的所有 VM。

本文介绍如何使用 Office 设置 FSLogix 应用程序容器。

## <a name="requirements"></a>要求

设置规则编辑器需要以下各项：

- 在未安装 Office 的情况下运行 Windows 的 VM
- Office 副本
- 部署上安装的 FSLogix 副本
- 主机池中的所有 VM 都对其具有只读访问权限的网络共享

## <a name="install-office"></a>安装 Office

若要在 VHD 或 VHDX 上安装 Office，请在 VM 中启用远程桌面协议，然后按照[在 VHD 主映像上安装 Office](install-office-on-wvd-master-image.md) 中的说明操作。 安装时，请确保使用[正确的许可证](overview.md#requirements)。

>[!NOTE]
>Windows 虚拟桌面需要共享计算机激活 (SCA)。

## <a name="install-fslogix"></a>安装 FSLogix

若要安装 FSLogix 和规则编辑器，请按照[下载并安装 FSLogix](/fslogix/install-ht) 中的说明操作。

## <a name="create-and-prepare-a-vhd-to-store-office"></a>创建和准备 VHD 以存储 Office

接下来，需要创建并准备 VHD 映像，以使用规则编辑器：

1. 以管理员身份打开命令提示符。 运行以下命令：

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > 请确保保留在此命令中看到的空格。

2. 接下来，运行以下命令：

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   如果找到该服务，请重新启动 VM，然后再继续执行步骤 3。

    ```cmd
    net stop ClickToRunSvc
    ```

3. 完成此操作后，请转到“Program Files” > “FSLogix” > “Apps”，并运行以下命令来创建目标 VHD：

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    用此命令创建的 VHD 应包含 C:\\Program Files\\Microsoft Office 文件夹。

    >[!NOTE]
    >如果看到任何错误，请卸载 Office 并从步骤 1 开始。

## <a name="configure-the-rule-editor"></a>配置规则编辑器

你已准备好映像，现在需要配置规则编辑器并创建文件来存储规则。

1. 转到“Program Files” > “FSLogix” > “Apps”，并运行 RuleEditor.exe。

2. 选择“文件” > “新建” > “创建”以创建新规则集，然后将该规则集保存到本地文件夹。

3. 选择“空白规则集”，然后选择“确定”。

4. 选择 + 按钮。 这将打开“添加规则”窗口。 这会更改“添加规则”对话框中的选项。

5. 从下拉菜单中，选择“应用容器 (VHD) 规则”。

6. 在“文件夹”字段中，输入 C:\\Program Files\\Microsoft Office。

7. 对于“磁盘文件”字段，请从“创建目标 VHD”部分选择“\<path\>\\office.vhd”。

8. 选择“确定”。

9. 转到 C:\\Users\\\<username\>\\Documents\\FSLogix Rule Sets 中的工作文件夹，并查找 .frx 和 .fxa 文件。 需要将这些文件移到位于 C:\\Program Files\\FSLogix\\Apps\\Rules 中的“规则”文件夹，规则才会开始工作。

10. 选择“将规则应用到系统”以使规则生效。

     >[!NOTE]
     > 需要将应用规则文件应用到所有会话主机。

## <a name="next-steps"></a>后续步骤

若要了解有关 FSLogix 的详细信息，请查看我们的 [FSLogix 文档](/fslogix/)。