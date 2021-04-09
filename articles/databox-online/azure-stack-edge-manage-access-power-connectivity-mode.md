---
title: Azure Stack Edge Pro 设备访问、电源和连接模式 | Microsoft Docs
description: 介绍如何管理帮助向 Azure 传输数据的 Azure Stack Edge Pro 设备的访问、电源和连接模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 240d3872536e6974d7f65eed22dace6816844e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200208"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro"></a>管理 Azure Stack Edge Pro 的访问、电源和连接模式

本文介绍如何管理 Azure Stack Edge Pro 的访问、电源和连接模式。 这些操作是通过本地 Web UI 或 Azure 门户执行的。

在本文中，学习如何：

> [!div class="checklist"]
> * 管理对设备的访问
> * 管理连接模式
> * 管理电源


## <a name="manage-device-access"></a>管理对设备的访问

可以通过使用设备密码来控制对 Azure Stack Edge Pro 设备的访问。 可以通过本地 Web UI 更改密码。 还可以在 Azure 门户中重置设备密码。

### <a name="change-device-password"></a>更改设备密码

在本地 UI 中遵循以下步骤更改设备密码。

1. 在本地 Web UI 中，转到“维护”>“密码更改”。
2. 依次输入当前密码和新密码。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 确认新密码。

    ![更改密码](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 选择“更改密码”。
 
### <a name="reset-device-password"></a>重置设备密码

重置工作流不需要用户回忆旧密码，在密码丢失时非常有用。 在 Azure 门户中执行此工作流。

1. 在 Azure 门户中，转到“概述”>“重置管理员密码”。

    ![重置密码](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 输入新密码并确认。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 选择“重置”。

    ![重置密码 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理资源访问

若要创建 Azure Stack Edge / Data Box Gateway、IoT 中心和 Azure 存储资源，需要具有资源组级别的参与者或更高权限。 还需要注册相应的资源提供程序。 对于涉及激活密钥和凭据的任何操作，还需要对 Microsoft Graph API 的权限。 这些在下面各部分中介绍。 

### <a name="manage-microsoft-graph-api-permissions"></a>管理 Microsoft Graph API 权限

为 Azure Stack Edge Pro 设备生成激活密钥或执行需要凭据的任何操作时，需要具有对 Azure Active Directory 图形 API 的权限。 需要凭据的操作可能是：

-  使用关联的存储帐户创建共享。
-  创建可访问设备上的共享的用户。

你应该对 Active Directory 租户具有 `User` 权限，因为你需要能够 `Read all directory objects`。 你不能使用“来宾”用户身份，因为这种用户没有 `Read all directory objects` 权限。 如果你是来宾，则生成激活密钥、在 Azure Stack Edge Pro 设备上创建共享、创建用户、配置 Edge 计算角色以及重置设备密码等操作都将失败。

如需详细了解如何向用户提供对 Microsoft Graph API 的访问权限，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference)。

### <a name="register-resource-providers"></a>注册资源提供程序

如果要在 Azure（在 Azure 资源管理器模型中）中预配资源，需要支持创建资源的资源提供程序。 例如，要预配虚拟机，订阅中应提供“Microsoft.Compute”资源提供程序。
 
资源提供程序在订阅级别注册。 默认情况下，任何新的 Azure 订阅都预注册到一组常用的资源提供程序中。 这组资源提供程序不包含适用于“Microsoft.DataBoxEdge”的资源提供程序。

你无需向订阅级别授予访问权限，用户可以在其拥有所有者权限的资源组中创建类似于“Microsoft.DataBoxEdge”的资源，前提是这些资源的资源提供程序已注册。

尝试创建任何资源之前，请确保在订阅中注册了资源提供程序。 如果未注册资源提供程序，则需要确保创建新资源的用户具有足够的权限在订阅级别上注册所需的资源提供程序。 如果还没有这样做，则会看到以下错误：

订阅\<Subscription name>没有注册资源提供程序 Microsoft.DataBoxEdge 的权限。


若要获取当前订阅中已注册资源提供程序的列表，请运行以下命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

对于 Azure Stack Edge Pro 设备，应注册 `Microsoft.DataBoxEdge`。 若要注册 `Microsoft.DataBoxEdge`，订阅管理员应运行以下命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

如需详细了解如何注册资源提供程序，请参阅[解决资源提供程序注册错误](../azure-resource-manager/templates/error-register-resource-provider.md)。

## <a name="manage-connectivity-mode"></a>管理连接模式

除了在默认的完全连接模式下运行以外，设备还可以在部分连接或完全断开连接模式下运行。 下面介绍上述每种模式：

- **完全连接** - 这是设备运行时的常规默认模式。 在此模式下，云上传和下载数据都处于启用状态。 可以使用 Azure 门户或本地 web UI 来管理设备。

- **部分连接** - 在此模式下，设备无法上传或下载任何共享数据，但可以通过 Azure 门户管理设备。

    使用按流量计费的卫星网络，并且目标是尽量减少网络带宽消耗时，通常会使用此模式。 执行设备监视操作时，仍有可能会消耗少量的网络带宽。

- **离线** – 在此模式下，设备完全与云断开连接，此时会禁用云上传和下载。 只能通过本地 Web UI 管理设备。

    想要使设备脱机时，通常会使用此模式。

若要更改设备模式，请执行以下步骤：

1. 在设备的本地 Web UI 中，转到“配置”>“云设置”。
2. 从下拉列表中，选择要操作设备的模式。 可以在“完全连接”、“部分连接”和“完全断开连接”中进行选择  。 若要在部分离线模式下运行设备，请启用“Azure 门户管理”。

    ![连接模式](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理电源

可使用本地 Web UI 关闭或重启物理设备。 在重启之前，建议使共享依次在数据服务器和设备上脱机。 此操作可以最大程度地减少发生数据损坏的可能性。

1. 在本地 Web UI 中，转到“维护”>“电源设置”。
2. 根据意图选择“关机”或“重启” 。

    ![电源设置](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 出现确认提示时，选择“是”以继续执行下一步操作。

> [!NOTE]
> 如果关闭物理设备，则需要按设备上的电源按钮将其打开。

## <a name="next-steps"></a>后续步骤

- 了解如何[管理共享](azure-stack-edge-manage-shares.md)。
