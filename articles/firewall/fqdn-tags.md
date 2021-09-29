---
title: Azure 防火墙的 FQDN 标记概述
description: FQDN 标记表示与已知的 Microsoft 服务关联的一组完全限定的域名 (FQDN)。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56f2a02109acd4f76cf5eb3b13dd70c878694f72
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604239"
---
# <a name="fqdn-tags-overview"></a>FQDN 标记概述

FQDN 标记表示与已知的 Microsoft 服务关联的一组完全限定的域名 (FQDN)。 可以在应用程序规则中使用 FQDN 标记，以允许所需出站网络流量通过防火墙。

例如，若要手动允许 Windows 更新网络流量通过防火墙，需要根据 Microsoft 文档创建多个应用程序规则。 使用 FQDN 标记，可以创建一个应用程序规则，其中包括 **Windows 更新** 标记，现在到 Microsoft Windows 更新终结点的网络流量可以流经防火墙。

你无法创建自己的 FQDN 标记，也无法指定标记中包含哪些 FQDN。 Microsoft 管理 FQDN 标记包含的 FQDN，并在 FQDN 更改时更新标记。 

<!--- screenshot of application rule with a FQDN tag.-->

下表显示了当前可使用的 FQDN 标记。 Microsoft 维护这些标记，你可以期望定期添加其他标记。

## <a name="current-fqdn-tags"></a>当前 FQDN 标记

|FQDN 标记  |说明  |
|---------|---------|
|Windows 更新     |允许出站访问 Microsoft 更新，如[如何为软件更新配置防火墙](/mem/configmgr/sum/get-started/install-a-software-update-point)中所述。|
|Windows 诊断|允许出站访问所有 [Windows 诊断终结点](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)。|
|Microsoft 主动保护服务 (MAPS)|允许出站访问 [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)。|
|应用服务环境 (ASE)|允许出站访问 ASE 平台流量。 此标记未涵盖由 ASE 创建的特定于客户的存储和 SQL 终结点。 这些应通过[服务终结点](../virtual-network/tutorial-restrict-network-access-to-resources.md)启用或手动添加。<br><br>有关将 Azure 防火墙与 ASE 集成的详细信息，请参阅[锁定应用服务环境](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)。|
|Azure 备份|允许对 Azure 备份服务进行出站访问。|
|Azure HDInsight|允许出站访问 HDInsight 平台流量。 此标记未涵盖特定于客户的存储和来自 HDInsight 的 SQL 流量。 使用[服务终结点](../virtual-network/tutorial-restrict-network-access-to-resources.md)启用这些项或手动添加它们。|
|WindowsVirtualDesktop (WVD)|允许出站 Windows 虚拟桌面平台流量。 此标记不包括由 WVD 创建的特定于部署的存储和服务总线终结点。 此外，还需要 DNS 和 KMS 网络规则。 有关将 Azure 防火墙与 WVD 集成的详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](protect-azure-virtual-desktop.md)。|
|Azure Kubernetes 服务 (AKS)|允许出站访问 AKS。 有关详细信息，请参阅[使用 Azure 防火墙保护 Azure Kubernetes 服务 (AKS) 部署](protect-azure-kubernetes-service.md)。|

> [!NOTE]
> 在应用程序规则中选择 FQDN 标记时，“协议:端口”字段必须设置为 **https**。

## <a name="next-steps"></a>后续步骤

若要了解如何部署 Azure 防火墙，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。