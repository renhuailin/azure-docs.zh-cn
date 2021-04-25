---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466109"
---
若要验证 VPN，可以创建一个只能由你创建的虚拟网络访问的存储帐户。 按照以下步骤创建存储帐户并将其与你创建的虚拟网络相关联：

1. 创建存储帐户。 可以使用计划与 Azure Stack Edge 设备一起使用的存储帐户。 尝试从外部网络（所选网络之外）访问存储帐户。 应可访问该存储帐户。
2. 在 Azure 门户中，转到该存储帐户。 
3. 转到“防火墙和虚拟网络”。 在“允许的访问来源”的右窗格中，选择“所选网络” 。 在“使用虚拟网络保护存储帐户”中，选择“+ 添加现有虚拟网络” 。

    ![验证 VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. 在“添加网络”边栏选项卡中：

    - 选择订阅。 这是与 Azure Stack Edge/Data Box Gateway 资源关联的相同订阅。 
    - 从下拉列表中，选择要与此存储帐户关联的虚拟网络。 选择你在上一步中创建的虚拟网络。
    - 在“子网”中，选择“默认”和“GatewaySubnet”。 将为此虚拟网络/子网组合启用服务终结点。 启用访问权限最长需要 15 分钟才能完成。
    - 选择“启用”。

    ![验证 VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. 保存“设置”。

    ![验证 VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 保存设置后，可以看到为其启用了虚拟网络的子网。

    ![验证 VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. 若要验证数据现在仅通过 VPN，请执行以下步骤： 
    - 尝试从外部网络（所选网络之外）访问存储帐户。 应不可访问该存储帐户。 
    - 尝试从在所选网络中启用的虚拟网络/子网访问存储帐户。 应可访问该存储帐户。 
 
只有启用了 VPN 才能访问此存储帐户。 如果禁用 VPN，还需要调整存储帐户的网络设置。 

有关详细信息，请转到[配置 Azure 存储防火墙和虚拟网络](../articles/storage/common/storage-network-security.md)。 

