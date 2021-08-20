---
title: 教程：为 Azure 网络功能管理器创建设备资源
description: 本教程介绍如何为 Azure 网络功能管理器创建设备资源。
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 048b086c407f5fbdc6239aec9dc509aceda7c8c2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468167"
---
# <a name="tutorial-create-a-network-function-manager-device-resource-preview"></a>教程：创建网络功能管理器设备资源（预览版）

在本教程中，你将为 Azure 网络功能管理器 (NFM) 创建设备资源。 该网络功能管理器设备资源会链接到 Azure Stack Edge 资源。 该设备资源聚合了 Azure Stack Edge 设备上部署的所有网络功能，并提供常用的服务，以用于在 Azure Stack Edge 上部署网络功能并对部署的所有网络功能进行监视、故障排除和一致的管理操作。 必须先创建网络功能管理器设备资源，然后才能将网络功能部署到 Azure Stack Edge 设备。

在本教程中，你将了解：

> [!div class="checklist"]
> * 验证先决条件
> * 创建设备资源
> * 获取注册密钥
> * 注册设备

## <a name="prerequisites"></a><a name="pre"></a>先决条件

验证以下先决条件：

* 满足[概述](overview.md#prereq)一文中列出的所有先决条件。
* 你有适当的权限。 有关详细信息，请参阅[资源提供程序注册和权限](overview.md#permissions)。
* 在创建设备资源之前，请查看 [区域可用性](overview.md#regions)部分。
* 验证是否可以通过 PowerShell 从 Windows 客户端远程连接到 Azure Stack Edge Pro GPU 设备。 有关详细信息，请参阅[连接到 PowerShell 接口](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

## <a name="create-a-device-resource"></a><a name="create"></a>创建设备资源

如果你已有 Azure 网络功能管理器设备资源，则不需要创建。 跳过本部分并转到[注册密钥](#key)部分。

若要创建设备资源，请使用以下步骤。

1. 使用你的 Microsoft Azure 凭据登录到 Azure [预览版门户](https://aka.ms/AzureNetworkFunctionManager)。

1. 在“基本信息”选项卡上，使用设备设置配置“项目详细信息”和“实例详细信息”  。
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="设备设置的屏幕截图。":::

   当你填写字段时，如果添加的字符通过了验证，则会出现一个绿色的打勾标记。 有些详细信息会由系统自动填充，而有些详细信息则是可自定义的字段：

   * 订阅：确认列出的订阅是否正确。 可以使用下拉列表更改订阅。
   * 资源组：选择现有资源组，或单击“新建”以创建一个新资源组 。
   * 区域：选择设备所在的位置。 此位置必须是受支持的区域。 有关详细信息，请参阅[区域可用性](overview.md#regions)。
   * 名称：输入设备的名称。
   * Azure Stack Edge：选择要注册为 Azure 网络功能管理器设备的 Azure Stack Edge 资源。 ASE 资源必须处于“联机”状态才能成功创建设备资源。 可以转到 Azure Stack Edge 资源页中的“属性”部分来检查 ASE 资源的状态。
1. 选择“查看 + 创建”以验证设备设置。
1. 验证所有设置后，选择“创建”。
   
   >[!NOTE]
   >网络功能管理器设备资源只能链接到一个 Azure Stack Edge 资源。 必须为每个 Azure Stack Edge 资源单独创建一个 NFM 设备资源。
   >

## <a name="get-the-registration-key"></a><a name="key"></a>获取注册密钥

1. 成功预配设备后，导航到设备资源部署到的资源组。
1. 单击该设备资源。 若要获取注册密钥，请单击“获取注册密钥”。 确保你有适当的权限，可生成注册密钥。 有关详细信息，请参阅[权限](overview.md#permissions)。

   :::image type="content" source="./media/create-device/register-device.png" alt-text="注册密钥的屏幕截图。" lightbox="./media/create-device/register-device.png":::
1. 请记下设备注册密钥，这在后续步骤中需要用到。

   > [!IMPORTANT]
   > 注册密钥在生成后的有效期为 24 小时。 如果注册密钥不再有效，可以重复本部分所述的步骤生成新的注册密钥。
   >

## <a name="register-the-device"></a><a name="registration"></a>注册设备

按照以下步骤将设备资源注册到 Azure Stack Edge。

1. 若要使用在上一步骤中获取的注册密钥注册设备，请[通过 Windows PowerShell 连接到 Azure Stack Edge 设备](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 将 PowerShell (minishell) 连接到设备后，请输入以下命令，其中将上一步骤中获取的设备注册密钥作为参数：
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. 验证设备资源的设备状态是否为“已注册”。

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="已注册的设备的屏幕截图。" lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署网络功能](deploy-functions.md)
