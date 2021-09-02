---
title: include 文件
description: 文件
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: b1eb954626570d7feb2af7fe0980e4f7a10e70c6
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113280126"
---
## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="for-windows"></a>对于 Windows

Windows OS 磁盘已使用 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 通用化。 如果后来更新或重新配置了 OS，则必须再次运行 sysprep。

> [!WARNING]
> 运行 sysprep 后，由于更新可能自动运行，因此，请在部署 VM 之前将其关闭。 此关闭操作可避免后续更新对操作系统或安装的服务做出特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 Windows VM](../../virtual-machines/generalize.md#windows)。

### <a name="for-linux"></a>对于 Linux

1. 删除 Azure Linux 代理。
    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入此命令：`sudo waagent –deprovision+user`。
    3. 键入 Y 以继续操作（可将“-force”参数添加到前一个命令，以避免确认步骤） 。
    4. 该命令完成后，请输入“退出”以关闭 SSH 客户端。
2. 停止虚拟机。
    1. 在 Azure 门户，选择资源组 (RG) 并取消分配 VM。
    2. VM 现已通用化，你可以使用此 VM 磁盘创建新的 VM。

### <a name="capture-image"></a>捕获映像

> [!NOTE]
> 包含 SIG 的 Azure 订阅必须与发布者帐户位于同一租户才能发布。 此外，发布者帐户必须至少具有对包含 SIG 的订阅的参与者访问权限。

VM 准备就绪后，即可在 Azure 共享映像库中捕获映像。 请按照以下步骤进行捕获：

1. 在 [Azure 门户](https://ms.portal.azure.com/)中转到你的虚拟机页。
2. 选择“捕获”。
3. 在“将映像共享到共享映像库”下，选择“是，将其作为映像版本共享到库” 。
4. 在“操作系统状态”下选择“通用化”。
5. 选择目标映像库，或“新建”。
6. 选择目标映像定义，或“新建”。
7. 提供映像的版本号。
8. 选择“查看 + 创建”可查看选择。
9. 传递验证后，选择“创建”。

## <a name="set-the-right-permissions"></a>设置适当的权限

如果合作伙伴中心帐户是托管共享映像库的订阅的所有者，则不需要进一步获取权限。

如果只有订阅的读取访问权限，请使用以下两个选项中的一个。

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>选项 1 - 要求所有者授予所有者权限

所有者授予所有者权限的步骤如下：

1. 转到共享映像库 (SIG)。
2. 在左侧窗格中选择“访问控制”(IAM)。
3. 依次选择“添加”、“添加角色分配”。 <br>
    :::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="已显示“添加角色分配”窗口。":::
1. 对于“角色”，请选择“所有者”。 
1. 对于“将访问权限分配给”，请选择“用户、组或服务主体”。 
1. 对于“选择”，输入映像发布者的 Azure 电子邮件。
1. 选择“保存”。

### <a name="option-two--run-a-command"></a>选项 2 - 运行命令

要求所有者运行其中任一命令（在任一情况下，使用创建共享映像库的订阅的 SusbscriptionId）。

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```
 
```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> 不需要生成 SAS URI，因为现在可以在合作伙伴中心发布 SIG 映像。 但是，如果仍需要参考 SAS URI 生成步骤，请参阅[如何为 VM 映像生成 SAS URI](../azure-vm-get-sas-uri.md)。
