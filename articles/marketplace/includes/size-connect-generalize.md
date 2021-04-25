---
title: include 文件
description: 文件
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629827"
---
## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="for-windows"></a>对于 Windows

Windows OS 磁盘已使用 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 通用化。 如果后来更新或重新配置了 OS，则必须再次运行 sysprep。

> [!WARNING]
> 运行 sysprep 后，由于更新可能自动运行，因此，请在部署 VM 之前将其关闭。 此关闭操作可避免后续更新对操作系统或安装的服务做出特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)。

### <a name="for-linux"></a>对于 Linux

以下过程将通用化 Linux VM，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](../../virtual-machines/linux/capture-image.md)。 当到达“从捕获的映像创建 VM”部分时，可以停止。

1. 删除 Azure Linux 代理。
    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入此命令：`sudo waagent –deprovision+user`。
    3. 键入 Y 以继续操作（可将“-force”参数添加到前一个命令，以避免确认步骤） 。
    4. 该命令完成后，请输入“退出”以关闭 SSH 客户端。
2. 停止虚拟机。
    1. 在 Azure 门户，选择资源组 (RG) 并取消分配 VM。
    2. VM 现已通用化，你可以使用此 VM 磁盘创建新的 VM。

### <a name="capture-image"></a>捕获映像

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

包含 SIG 的 Azure 订阅必须与发布者帐户位于同一租户才能发布。 此外，发布者帐户必须具有对 SIG 的所有者访问权限。 

若要授予访问权限，请执行以下操作：

1. 转到共享映像库。
2. 在左窗格中，选择“访问控制”(IAM)。
3. 依次选择“添加”和“添加角色分配” 。
4. 选择“角色”或“所有者” 。
5. 在“分配访问权限至”下，选择“用户、组或服务主体” 。
6. 选择将发布映像的人员的 Azure 电子邮件。
7. 选择“保存”。

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="显示“添加角色分配”窗口。":::

> [!NOTE]
> 不需要生成 SAS URI，因为现在可以在合作伙伴中心发布 SIG 映像。 但是，如果仍需要参考 SAS URI 生成步骤，请参阅[如何为 VM 映像生成 SAS URI](../azure-vm-get-sas-uri.md)。
