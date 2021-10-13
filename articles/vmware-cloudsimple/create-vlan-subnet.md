---
title: 创建 VLAN/子网 - Azure VMware Solution by CloudSimple
description: Azure VMware Solutions by CloudSimple - 介绍如何为私有云创建和管理 VLAN/子网，然后应用防火墙规则。
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ca6848f228f1bbe874faee1ad8165851fc99ab6d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611427"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>为私有云创建和管理 VLAN/子网

打开“网络”页上的“VLAN/子网”选项卡，为私有云创建和管理 VLAN/子网。 创建 VLAN/子网之后，可应用防火墙规则。

## <a name="create-a-vlansubnet"></a>创建 VLAN/子网

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)，并在侧边菜单中选择“网络”。
2. 选择“VLAN/子网”。
3. 单击“创建 VLAN/子网”。

    ![VLAN/子网页](media/vlan-subnet-page.png)

4. 选择新 VLAN/子网的私有云。
5. 输入 VLAN ID。
6. 输入子网名称。
7. 要在 VLAN（子网）上启用路由，请指定子网 CIDR 范围。 请确保 CIDR 范围与任何本地子网、Azure 子网或网关子网均不重叠。
8. 单击“提交”  。

    ![创建 VLAN/子网](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 每个私有云可配置 30 个 VLAN。 如需提高限额，可[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>使用 VLAN 信息在 vSphere 中设置分布式端口组

要在 vSphere 中创建分布式端口组，请按照《<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">VSphere 网络指南</a>》中 VMware 主题“添加分布式端口组”中的说明执行操作。 设置分布式端口组时，请提供 CloudSimple 配置中的 VLAN 信息。

![分布式端口组](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>选择防火墙表

有关防火墙表和关联规则的定义，请参阅“网络”>“防火墙表”页。 要选择防火墙表以应用于私有云的 VLAN/子网，请选择“VLAN/子网”，单击“VLAN/子网”页上的“防火墙表附件” 。 有关设置防火墙表和定义规则的说明，请参阅[防火墙表](firewall.md)。

![防火墙表](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 一个子网可与一个防火墙表相关联。 一个防火墙表可与多个子网相关联。

## <a name="edit-a-vlansubnet"></a>编辑 VLAN/子网

要编辑 VLAN/子网的设置，请在“VLAN/子网”页上选择该设置，然后单击“编辑”图标。 进行更改，然后单击“提交”。

## <a name="delete-a-vlansubnet"></a>删除 VLAN/子网

要删除 VLAN/子网，请在“VLAN/子网”页上选择它，然后单击“删除”图标。 单击“删除”进行确认。
