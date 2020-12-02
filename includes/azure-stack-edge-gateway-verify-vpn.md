---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466109"
---
若要验证 VPN，你可以创建一个只能由你创建的虚拟网络访问的存储帐户。 按照以下步骤创建存储帐户并将其与你创建的虚拟网络相关联：

1. 创建存储帐户。 你可以使用计划与 Azure Stack Edge 设备一起使用的存储帐户。 尝试从所选网络) 之外的外部网络访问存储帐户 (。 应可访问该存储帐户。
2. 在 Azure 门户中，请参阅存储帐户。 
3. 中转到 " **防火墙和虚拟网络**"。 在 **允许从其访问** 的右窗格中，选择 " **所选网络**"。 在 " **通过虚拟网络保护存储帐户**" 中，选择 " **+ 添加现有虚拟网络"。**

    ![验证 VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. 在 " **添加网络** " 边栏选项卡中：

    - 选择订阅。 这与 Azure Stack 边缘/Data Box Gateway 资源关联的订阅相同。 
    - 从下拉列表中，选择要与此存储帐户关联的虚拟网络。 选择你在上一步中创建的虚拟网络。
    - 在 " **子网**" 中，选择 "*_默认_* _" 和 "_GatewaySubnet"。 将为此虚拟网络/子网组合启用服务终结点。 启用访问最多需要15分钟才能完成。
    - 选择“启用”。

    ![验证 VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. 保存 **设置**。

    ![验证 VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 保存设置后，可以看到为其启用虚拟网络的子网。

    ![验证 VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. 若要验证数据现在仅通过 VPN，请执行以下步骤： 
    - 尝试从所选网络) 之外的外部网络访问存储帐户 (。 存储帐户不应是可访问的。 
    - 尝试从在所选网络中启用的虚拟网络/子网访问存储帐户。 应可访问该存储帐户。 
 
只有启用了 VPN，才能访问此存储帐户。 如果禁用 VPN，还需要调整存储帐户的网络设置。 

有关详细信息，请参阅 [配置 Azure 存储防火墙和虚拟网络](../articles/storage/common/storage-network-security.md)。 

