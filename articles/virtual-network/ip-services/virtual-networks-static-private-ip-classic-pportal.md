---
title: 为 VM（经典）配置专用 IP 地址 - Azure 门户 | Microsoft 文档
description: 了解如何使用 Azure 门户为虚拟机（经典）配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: allensu
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e79d4740e6371c68ef57fa7eb9bb57a8d52ae97
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367364"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>使用 Azure 门户为虚拟机（经典）配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以 [管理 Resource Manager 部署模型中的静态专用 IP 地址](virtual-networks-static-private-ip-arm-pportal.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../../includes/virtual-networks-static-ip-scenario-include.md)]

后面的示例步骤需要一个已创建的简单环境。 如果想要运行本文档中所显示的步骤，首先需要生成 [创建 Vnet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)中所述的测试环境。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何在创建 VM 时指定静态专用 IP 地址
若要在名为 *TestVNet* 的 VNet 的 *FrontEnd* 子网中使用静态专用 IP *192.168.1.101* 创建名为 *DNS01* 的 VM，请完成以下步骤：

1. 在浏览器中导航到 https://portal.azure.com ，并在必要时使用 Azure 帐户登录。
2. 选择“新建” > “计算” > “Windows Server 2012 R2 Datacenter”（注意“选择部署模型”列表已显示“经典”），然后选择“创建”。     
   
    ![显示突出显示“新建”>“计算”>“Windows Server 2012 R2 数据中心”磁贴的 Azure 门户的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. 在“创建 VM”下，输入要创建的 VM 的名称（在此方案中为 *DNS01*）、本地管理员帐户和密码。
   
    ![显示如何通过键入 VM 名称、本地管理员用户名和密码来创建 VM 的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. 选择“可选配置” > “网络” > “虚拟网络”，然后选择“TestVNet”。    如果 **TestVNet** 不可用，请确保使用的位置为“*Central US*”，并已创建本文开头所述的测试环境。
   
    ![显示“可选配置”>“网络”>“虚拟网络”>“TestVNet 选项”的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. 在“网络”下，确保当前选定的子网为“FrontEnd”，选择“IP 地址”，在“IP 地址分配”下选择“静态”，并输入 *192.168.1.101* 作为“IP 地址”，如下所示。   
   
    ![突出显示键入静态 IP 地址的“IP 地址”字段的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. 在“IP 地址”下选择“确定”，在“网络”下选择“确定”，然后在“可选配置”下选择“确定”。     
7. 在“创建 VM”下，选择“创建”。  注意，以下磁贴会显示在仪表板中：
   
    ![显示“正在创建 Windows Server 2012 R2 数据中心”磁贴的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何检索 VM 的静态专用 IP 地址信息
若要查看使用以上步骤创建的 VM 的静态专用 IP 地址信息，请执行以下步骤。

1. 在 Azure 门户中，选择“浏览全部” > “虚拟机（经典）” > “DNS01” > “所有设置” > “IP 地址”，并请注意如下所示的 IP 地址分配和 IP 地址。    
   
    ![在 Azure 门户中创建 VM](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何从 VM 中删除静态专用 IP 地址

在“IP 地址”下，在“IP 地址分配”右侧选择“动态”，选择“保存”，然后选择“是”，如下图中所示：
   
![显示如何通过选择 IP 地址分配标签右侧的“动态”从 VM 中删除静态专用 IP 地址的屏幕截图。](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何将静态专用 IP 地址添加到现有 VM

1. 在前面显示的“IP 地址”下，在“IP 地址分配”右侧选择“静态”。
2. 对于“IP 地址”，键入 *192.168.1.101*，选择“保存”，然后选择“是”。  

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

我们建议，除非有必要，否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它是分配给 Azure VM 的同一专用 IP 地址，否则可能会丢失与虚拟机的连接。 切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。

## <a name="next-steps"></a>后续步骤
* 了解[保留公共 IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) 地址。
* 了解[实例层级公共 IP (ILPIP) 地址](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip)。
* 查阅[保留 IP REST API](/previous-versions/azure/reference/dn722420(v=azure.100))。